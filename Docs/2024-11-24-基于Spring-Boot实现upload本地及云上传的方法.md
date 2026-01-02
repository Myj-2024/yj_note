# 基于Spring Boot实现upload本地及云上传的方法


> 参考项目：GitHub [@xkcoding](https://github.com/xkcoding)大佬的 [spring-boot-demo](https://github.com/xkcoding/spring-boot-demo)项目。

在原来项目的基础上，将七牛云上传修改成Minio上传。

## 一、项目目录结构

```bash
 Minio-demo
    ├─.idea
    ├─src
    │  ├─main
    │  │  ├─java
    │  │  │  └─com
    │  │  │      └─example
    │  │  │          ├─config
    │  │  │          ├─Controller
    │  │  │          ├─pojo
    │  │  │          └─utils
    │  │  └─resources
    │  │      └─templates
    │  └─test
    │      └─java
    │          └─com
    │              └─example
    └─target
```



## 二、实现方法

### 本地上传的具体实现方法

#### `Controller`层的具体代码

```java
package com.example.Controller;

import cn.hutool.core.date.DateUtil;
import cn.hutool.core.lang.Dict;
import cn.hutool.core.util.StrUtil;
import com.example.pojo.Result;
import com.example.utils.MinioOperator;  // 替换为MinIO工具类
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import java.io.File;


@Slf4j
@RestController
@RequestMapping("/upload")
public class UploadController {

    @Value("${spring.servlet.multipart.location}")
    private String fileTempPath;

    @PostMapping(value = "/local",consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    public Dict local(@RequestParam("file")MultipartFile file){
        if (file.isEmpty()){
            return Dict.create().set("code", 400).set("msg", "上传文件不能为空");
        }
        String fileName = file.getOriginalFilename();
        String rawFileName = StrUtil.subBefore(fileName, ".", true);
        String fileType = StrUtil.subAfter(fileName, ".", true);
        String localFilePath = StrUtil.appendIfMissing(fileTempPath, "/") + rawFileName + "-" + DateUtil.current() + "." + fileType;
        try {
            file.transferTo(new File(localFilePath));
        } catch (Exception e) {
            log.error("文件上传失败：{}", e.getMessage());
            return Dict.create().set("code", 500).set("msg", "上传文件失败");
        }
        log.info("【文件上传至本地】绝对路径：{}", localFilePath);
        return Dict.create().set("code", 200).set("message", "上传成功").set("data", Dict.create().set("fileName", fileName).set("filePath", localFilePath));
    }
```

#### **配置类**

```java
package com.example.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

/**
 * 后端统一返回结果
 */
@Data
public class Result {

    private Integer code; //编码：1成功，0为失败
    private String msg; //错误信息
    private Object data; //数据

    public static Result success() {
        Result result = new Result();
        result.code = 1;
        result.msg = "success";
        return result;
    }

    public static Result success(Object object) {
        Result result = new Result();
        result.data = object;
        result.code = 1;
        result.msg = "success";
        return result;
    }

    public static Result error(String msg) {
        Result result = new Result();
        result.msg = msg;
        result.code = 0;
        return result;
    }

}
```

### MInio文件上传的具体实现方法

#### `Controller`层的具体代码

```java
@Autowired
    private MinioOperator minioOperator;  // 注入MinIO工具类

    @PostMapping(value = "/yun", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    public Result upload(MultipartFile file) throws Exception {
        log.info("文件上传：{}", file.getOriginalFilename());
        // 调用MinIO工具类上传文件
        String url = minioOperator.upload(file.getBytes(), file.getOriginalFilename());
        log.info("文件上传完成，返回访问路径：{}", url);
        return Result.success(url);
    }
```

#### 基于本项目（`utils`层）的具体代码

```java
package com.example.utils;

import io.minio.MinioClient;
import io.minio.PutObjectArgs;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.io.ByteArrayInputStream;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.UUID;

@Component
public class MinioOperator {

    @Autowired
    private MinioClient minioClient;

    // 硬编码配置值
    private final String bucketName = "my-project";  // 替换为实际桶名
    private final String endpoint = "http://192.168.43.20:19966";

    /**
     * 上传文件到MinIO
     *
     * @param content          文件字节数组
     * @param originalFilename 原始文件名（用于获取文件后缀）
     * @return 文件在MinIO中的访问URL
     */
    public String upload(byte[] content, String originalFilename) throws Exception {
        // 1. 校验文件
        if (content == null || content.length == 0) {
            throw new IllegalArgumentException("上传文件不能为空");
        }
        if (originalFilename == null || !originalFilename.contains(".")) {
            throw new IllegalArgumentException("文件名格式错误");
        }

        // 2. 生成文件存储路径（按日期分类 + 随机文件名避免重复）
        String dateDir = LocalDate.now().format(DateTimeFormatter.ofPattern("yyyy/MM/dd"));
        String fileSuffix = originalFilename.substring(originalFilename.lastIndexOf("."));
        String fileName = UUID.randomUUID().toString().replace("-", "") + fileSuffix;
        String objectName = dateDir + "/" + fileName;  // 最终存储路径（如：2024/06/15/xxx.jpg）

        // 3. 上传文件到MinIO（适配新版SDK）
        minioClient.putObject(
                PutObjectArgs.builder()
                        .bucket(bucketName)  // 桶名称
                        .object(objectName)  // 文件存储路径
                        .stream(new ByteArrayInputStream(content), content.length, -1)  // 文件流（-1表示自动识别大小）
                        .contentType(getContentType(fileSuffix)) // 优化：设置正确的文件类型
                        .build()
        );

        // 4. 生成访问URL（关键修复：直接拼接endpoint，避免调用SDK的getEndpoint()）
        // 处理endpoint末尾的斜杠，避免URL拼接错误
        String baseUrl = endpoint.endsWith("/") ? endpoint : endpoint + "/";
        String accessUrl = baseUrl + bucketName + "/" + objectName;

        return accessUrl;
    }

    /**
     * 优化：根据文件后缀设置ContentType，避免浏览器下载而非预览
     */
    private String getContentType(String suffix) {
        String contentType;
        switch (suffix.toLowerCase()) {
            case ".jpg":
            case ".jpeg":
            case ".png":
            case ".gif":
                contentType = "image/" + suffix.substring(1);
                break;
            case ".pdf":
                contentType = "application/pdf";
                break;
            case ".txt":
                contentType = "text/plain";
                break;
            case ".mp4":
                contentType = "video/mp4";
                break;
            default:
                contentType = "application/octet-stream";
        }
        return contentType;
    }
}
```

#### config层的具体代码

```java
package com.example.config;

import io.minio.MinioClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MinioConfig {

    private final String endpoint;
    private final String accessKey;
    private final String secretKey;

    public MinioConfig(
            @Value("${minio.url}") String endpoint,
            @Value("${minio.accessKey}") String accessKey,
            @Value("${minio.secretKey}") String secretKey) {
        this.endpoint = endpoint;
        this.accessKey = accessKey;
        this.secretKey = secretKey;
    }

    @Bean
    public MinioClient minioClient() {
        return MinioClient.builder()
                .endpoint(endpoint)
                .credentials(accessKey, secretKey)
                .build();
    }
}
```

#### pom.xml中需要引入的依赖

```xml
<dependency>
            <groupId>io.minio</groupId>
            <artifactId>minio</artifactId>
            <version>8.2.2</version>
        </dependency>

<dependency>
            <groupId>cn.hutool </groupId>
            <artifactId>hutool-all </artifactId>
            <version>5.8.40</version>
        </dependency>
```

静态页面（本项目基于`resources`下的`templates`包中创建的`index.html`）

```html
<!doctype html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<meta name="viewport"
	      content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<title>spring-boot-demo-upload</title>
	<!-- import Vue.js -->
	<script src="https://cdn.bootcss.com/vue/2.5.17/vue.min.js"></script>
	<!-- import stylesheet -->
	<link href="https://cdn.bootcss.com/iview/3.1.4/styles/iview.css" rel="stylesheet">
	<!-- import iView -->
	<script src="https://cdn.bootcss.com/iview/3.1.4/iview.min.js"></script>
</head>
<body>
<div id="app">
	<Row :gutter="16" style="background:#eee;padding:10%">
		<i-col span="12">
			<Card style="height: 300px">
				<p slot="title">
					<Icon type="ios-cloud-upload"></Icon>
					本地上传
				</p>
				<div style="text-align: center;">
					<Upload
							:before-upload="handleLocalUpload"
							action="/demo/upload/local"
							ref="localUploadRef"
							:on-success="handleLocalSuccess"
							:on-error="handleLocalError"
					>
						<i-button icon="ios-cloud-upload-outline">选择文件</i-button>
					</Upload>
					<i-button
							type="primary"
							@click="localUpload"
							:loading="local.loadingStatus"
							:disabled="!local.file">
						{{ local.loadingStatus ? '本地文件上传中' : '本地上传' }}
					</i-button>
				</div>
				<div>
					<div v-if="local.log.status != 0">状态：{{local.log.message}}</div>
					<div v-if="local.log.status === 200">文件名：{{local.log.fileName}}</div>
					<div v-if="local.log.status === 200">文件路径：{{local.log.filePath}}</div>
				</div>
			</Card>
		</i-col>
		<i-col span="12">
			<Card style="height: 300px;">
				<p slot="title">
					<Icon type="md-cloud-upload"></Icon>
					Minio上传
				</p>
				<div style="text-align: center;">
					<Upload
							:before-upload="handleYunUpload"
							action="/demo/upload/yun"
							ref="yunUploadRef"
							:on-success="handleYunSuccess"
							:on-error="handleYunError"
					>
						<i-button icon="ios-cloud-upload-outline">选择文件</i-button>
					</Upload>
					<i-button
							type="primary"
							@click="yunUpload"
							:loading="yun.loadingStatus"
							:disabled="!yun.file">
						{{ yun.loadingStatus ? 'Minio文件上传中' : 'Minio上传' }}
					</i-button>
				</div>
				<div>
					<div v-if="yun.log.status != 0">状态：{{yun.log.message}}</div>
					<div v-if="yun.log.status === 200">文件名：{{yun.log.fileName}}</div>
					<div v-if="yun.log.status === 200">文件路径：{{yun.log.filePath}}</div>
				</div>
			</Card>
		</i-col>
	</Row>
</div>
<script>
	new Vue({
		el: '#app',
		data: {
			local: {
				// 选择文件后，将 beforeUpload 返回的 file 保存在这里，后面会用到
				file: null,
				// 标记上传状态
				loadingStatus: false,
				log: {
					status: 0,
					message: "",
					fileName: "",
					filePath: ""
				}
			},
			yun: {
				// 选择文件后，将 beforeUpload 返回的 file 保存在这里，后面会用到
				file: null,
				// 标记上传状态
				loadingStatus: false,
				log: {
					status: 0,
					message: "",
					fileName: "",
					filePath: ""
				}
			}
		},
		methods: {
			// beforeUpload 在返回 false 或 Promise 时，会停止自动上传，这里我们将选择好的文件 file 保存在 data里，并 return false
			handleLocalUpload(file) {
				this.local.file = file;
				return false;
			},
			// 这里是手动上传，通过 $refs 获取到 Upload 实例，然后调用私有方法 .post()，把保存在 data 里的 file 上传。
			// iView 的 Upload 组件在调用 .post() 方法时，就会继续上传了。
			localUpload() {
				this.local.loadingStatus = true;  // 标记上传状态
				this.$refs.localUploadRef.post(this.local.file);
			},
			// 上传成功后，清空 data 里的 file，并修改上传状态
			handleLocalSuccess(response) {
				this.local.file = null;
				this.local.loadingStatus = false;
				if (response.code === 200) {
					this.$Message.success(response.message);
					this.local.log.status = response.code;
					this.local.log.message = response.message;
					this.local.log.fileName = response.data.fileName;
					this.local.log.filePath = response.data.filePath;
					this.$refs.localUploadRef.clearFiles();
				} else {
					this.$Message.error(response.message);
					this.local.log.status = response.code;
					this.local.log.message = response.message;
				}
			},
			// 上传失败后，清空 data 里的 file，并修改上传状态
			handleLocalError() {
				this.local.file = null;
				this.local.loadingStatus = false;
				this.$Message.error('上传失败');
			},
			// beforeUpload 在返回 false 或 Promise 时，会停止自动上传，这里我们将选择好的文件 file 保存在 data里，并 return false
			handleYunUpload(file) {
				this.yun.file = file;
				return false;
			},
			// 这里是手动上传，通过 $refs 获取到 Upload 实例，然后调用私有方法 .post()，把保存在 data 里的 file 上传。
			// iView 的 Upload 组件在调用 .post() 方法时，就会继续上传了。
			yunUpload() {
				this.yun.loadingStatus = true;  // 标记上传状态
				this.$refs.yunUploadRef.post(this.yun.file);
			},
			// 上传成功后，清空 data 里的 file，并修改上传状态
			handleYunSuccess(response) {
				this.yun.file = null;
				this.yun.loadingStatus = false;
				if (response.code === 200) {
					this.$Message.success(response.message);
					this.yun.log.status = response.code;
					this.yun.log.message = response.message;
					this.yun.log.fileName = response.data.fileName;
					this.yun.log.filePath = response.data.filePath;
					this.$refs.yunUploadRef.clearFiles();
				} else {
					this.$Message.error(response.message);
					this.yun.log.status = response.code;
					this.yun.log.message = response.message;
				}
			},
			// 上传失败后，清空 data 里的 file，并修改上传状态
			handleYunError() {
				this.yun.file = null;
				this.yun.loadingStatus = false;
				this.$Message.error('上传失败');
			}
		}
	})
</script>
</body>
</html>
```

**`yml`配置文件**

```properties
server:
  port: 8080
  servlet:
    context-path: /demo

spring:
  servlet:
    multipart:
      enabled: true
      location: D:\image
      file-size-threshold: 5MB
      max-file-size: 20MB
  web:
    resources:
      static-locations: classpath:/static/,classpath:/public/,classpath:/resources/,classpath:/templates/
  mvc:
    static-path-pattern: /**  # 允许匹配所有静态资源路径（默认就是/**，可显式配置）

minio:
  url: http://192.168.43.20:19966  # 缩进2个空格（与accessKey对齐）
  accessKey: your-accessKey               # 同层级缩进一致
  secretKey: your-accessKey  
  bucket-name: your-bucket-name
```



## 三、学到的知识

### Git仓库部署

**具体实践方法如下：**

#### 1.前提：

1. 已有Github账号，且下载安装好Git
2. 将Github账号与本地Git关联
3. 将本地需要上传的文件夹整理好，在Github上创建好空仓库

#### 2.实现方法：

1. 在本地文件夹内右击选择“`Open Git Bash here`”。
2. `git init`完成初始化后，会在该文件夹中多出一个隐藏文件夹（.git）。
3. `git branch -M main`将原Git中的master改为现在的main。
4. `git remote add origin` https://github.com/创建好的空资源库链接,将本地文件夹与Github指定资源库链接。
5. `git add .`注意`add`后有一空格，表示上传所有该文件夹中的文件，也可单独选择文件上传。
6. `git commit -m "first upload"`为上传添加注释。
7.  `git push -u origin main`。Push到Github上。首次需要加 `-u` 。如果Github的资源库不为空，如在创建时添加了README文件，则需要先执行以下代码将二者同步。

```bash
git pull --rebase origin main
```

再执行`push`。

```bash
git push origin main
```

8. `git status`

## 四、一些问题

1. 项目启动时目录结构发生变化

   原因是原项目中依赖中Hutool未表明版本，只需要添加` Hutool`的版本（本项目版本为`v5.8.40`）即可。

2. 将原来的`Minio-demo`的项目以模块的形式添加到`Java-Practice-Demo`项目中，导致无法访问静态资源

   **解决方法：**

   在yml配置文件中添加一下配置：

   ```yaml
     web:
       resources:
         static-locations: classpath:/static/,classpath:/public/,classpath:/resources/,classpath:/templates/
     mvc:
       static-path-pattern: /**  # 允许匹配所有静态资源路径（默认就是/**，可显式配置）
   ```

3. [idea创建Spring Boot项目时版本只有Java21和Java17选项](https://www.cnblogs.com/AndroidJotting/p/18398181)

   idea如果版本高了就会出现在创建Springboot项目时只有Java21和Java17选项
   
   选择jdk1.8的时候很可能出现下图报错，这是因为版本jdk1.8与Java17不兼容
   
   ![img](https://bu.dusays.com/2026/01/02/695781e305dee.png)
   
   #### 1、替换下载数据源
   
   可以将`https://start.spring.io/ `替换成 `https://start.aliyun.com`阿里云的下载地址
   
   ![img](https://bu.dusays.com/2026/01/02/695781df25a4a.png)
   
   ![img](https://bu.dusays.com/2026/01/02/695781d019ae6.png)
   
   #### 2、直接根据Java21或者Java17创建项目
   
   创建项目后，将pom.xml文件里面的版本改成Java8
   
   ![img](https://bu.dusays.com/2026/01/02/695781ebc7500.png)