---
5title: Java经典算法50题
date: 2024-11-24 19:47:12
updated: 2024-11-24 19:47:12
tags: [Java]
categories: [后端,Java经典算法50题]
description: Java经典算法50题
cover: 
swiper_index: 6#置顶轮播图顺序，非负整数，数字越大越靠前
---

【程序1】  
题目：古典问题：有一对兔子，从出生后第3个月起每个月都生一对兔子，小兔子长到第三个月后每个月又生一对兔子，假如兔子都不死，问每个月的兔子总数为多少？

```java
//这是一个菲波拉契数列问题
public class test01 {
    public static void main(String[] args) {
        int f1=1,f2=1,f;
        int M=30;
        System.out.println(f1);
        System.out.println(f2);
        for(int i=3;i<M;i++) {
            f=f2;
            f2=f1+f2;
            f1=f;
            System.out.println(f2);
        }
    }
}
```

【程序2】  
题目：判断101-200之间有多少个素数，并输出所有素数。
程序分析：判断素数的方法：用一个数分别去除2到sqrt(这个数)，如果能被整除， 则表明此数不是素数，反之是素数。  

```java
public class test02 {
    public static void main(String[] args) {
        int count=0;
        for(int i=101;i<200;i+=2) {
            boolean flag=true;
            for(int j=2;j<=Math.sqrt(i);j++) {
                if(i%j==0) {
                    flag=false;
                    break;
                }
            }
            if(flag==true) {
                count++;
                System.out.println(i);
            }
        }
        System.out.println(count);
    }
}
```

【程序3】  
题目：打印出所有的 "水仙花数 "，所谓 "水仙花数 "是指一个三位数，其各位数字立方和等于该数本身。例如：153是一个 "水仙花数 "，因为153=1的三次方＋5的三次方＋3的三次方。

```java
public class test03 {
    public static void main(String[] args) {
        int a,b,c;
        for(int i=101;i<1000;i++) {
            a=i%10;
            b=i/10%10;
            c=i/100;
            if(a*a*a+b*b*b+c*c*c==i)
                System.out.println(i);
            }
        }
} 
```

【程序4】  
题目：将一个正整数分解质因数。例如：输入90,打印出90=2*3*3*5。  
程序分析：对n进行分解质因数，应先找到一个最小的质数k，然后按下述步骤完成：  
(1)如果这个质数恰等于n，则说明分解质因数的过程已经结束，打印出即可。  
(2)如果n > k，但n能被k整除，则应打印出k的值，并用n除以k的商,作为新的正整数你n,重复执行第一步。  
(3)如果n不能被k整除，则用k+1作为k的值,重复执行第一步。 

```java
import java.util.Scanner;
public class test04 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int n=input.nextInt();
        int k=2;
        while(n>=k) {
            if(n==k) {
                System.out.println(k);
                break;
            }else if (n%k==0) {
                System.out.println(k);
                n=n/k;
            }else {
                k++;
            }
        }
    }
}
```

【程序5】  
题目：利用条件运算符的嵌套来完成此题：学习成绩> =90分的同学用A表示，60-89分之间的用B表示，60分以下的用C表示。

```java
import java.util.Scanner;
public class test05 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int score=input.nextInt();
        char grade=score>=90?'A':score>=60?'B':'C';
        System.out.println(grade);
    }
}
```

【程序6】  
题目：输入两个正整数m和n，求其最大公约数和最小公倍数。 

```java
/**在循环中，只要除数不等于0，用较大数除以较小的数，将小的一个数作为下一轮循环的大数，取得的余数作为下一轮循环的较小的数，如此循环直到较小的数的值为0，返回较大的数，此数即为最大公约数，最小公倍数为两数之积除以最大公约数。**/
import java.util.Scanner;
public class test06 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int a=input.nextInt();
        int b=input.nextInt();
        test06 test=new test06();
        int i = test.gongyinshu(a, b);
        System.out.println("最小公因数"+i);
        System.out.println("最大公倍数"+a*b/i);
    }
    public int gongyinshu(int a,int b) {
        if(a<b) {
            int t=b;
            b=a;
            a=t;
        }
        while(b!=0) {
            if(a==b) 
                return a;
            int x=b;
            b=a%b;
            a=x;
        }
        return a;
    }
}
```

【程序7】  
题目：输入一行字符，分别统计出其中英文字母、空格、数字和其它字符的个数。 

```java
import java.util.Scanner;
public class test07 {
    public static void main(String[] args) {
        int abccount=0;
        int spacecount=0;
        int numcount=0;
        int othercount=0;
        Scanner input=new Scanner(System.in);
        String toString=input.nextLine();
        char [] ch=toString.toCharArray();
 
        for(int i=0;i<ch.length;i++) {
            if(Character.isLetter(ch[i])) {
                abccount++;
            }else if(Character.isDigit(ch[i])) {
                numcount++;
            }else if(Character.isSpaceChar(ch[i])){
                spacecount++;
            }else {
                othercount++;
            }
        }
        System.out.println(abccount);
        System.out.println(spacecount);
        System.out.println(numcount);
        System.out.println(othercount);
    }
}
```

【程序8】  
题目：求s=a+aa+aaa+aaaa+aa...a的值，其中a是一个数字。例如2+22+222+2222+22222(此时共有5个数相加)，几个数相加有键盘控制。  

```java
import java.util.Scanner;
public class test08 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int a=input.nextInt();
        int n=input.nextInt();
        int sum=0,b=0;
        for(int i=0;i<n;i++) {
            b+=a;
            sum+=b;
            a=a*10;
        }
    System.out.println(sum);
    }
}
```

【程序9】  
题目：一个数如果恰好等于它的因子之和，这个数就称为 "完数 "。例如6=1＋2＋3.编程   找出1000以内的所有完数。  

```java
public class test09 {
    public static void main(String[] args) {
        for(int i=1;i<=1000;i++) {
            int t = 0;
            for(int j=1;j<=i/2;j++) {
                if(i%j==0) {
                    t+=j;
                }
            }
            if(t==i) {
                System.out.println(i);
            }
        }
    }
}
```

【程序10】  
题目：一球从100米高度自由落下，每次落地后反跳回原高度的一半；再落下，求它在 第10次落地时，共经过多少米？第10次反弹多高？

```java
public class test10 {
    public static void main(String[] args) {
        double h=100;
        double s=100;
        for(int i=1;i<=10;i++) {
            h=h/2;
            s=s+2*h;
        }
        System.out.println(s);
        System.out.println(h);
    }
}
```

【程序11】  
题目：有1、2、3、4四个数字，能组成多少个互不相同且一个数字中无重复数字的三位数？并把他们都输入。

```java
public class test11 {
    public static void main(String[] args) {
        int count=0;
        for(int i=1;i<5;i++) {
            for(int j=1;j<5;j++) {
                for(int k=1;k<5;k++) {
                    if(i!=j&&j!=k&&i!=k) {
                        count++;
                        System.out.println(i*100+j*10+k);
                    }
                }
            }
        }
        System.out.println(count);
    }
}
```

【程序12】  
题目：企业发放的奖金根据利润提成。利润(I)低于或等于10万元时，奖金可提10%；利润高于10万元，低于20万元时，低于10万元的部分按10%提成，高于10万元的部分，可可提成7.5%；20万到40万之间时，高于20万元的部分，可提成5%；40万到60万之间时高于40万元的部分，可提成3%；60万到100万之间时，高于60万元的部分，可提成1.5%，高于100万元时，超过100万元的部分按1%提成，从键盘输入当月利润，求应发放奖金总数？

```java
import java.util.Scanner;
public class test12 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        double x=input.nextDouble();
        double y=0;
        if(x>0&&x<=10) {
            y=x*0.1;
        }else if (x>10&&x<=20) {
            y=10*0.1+(x-10)*0.075;
        }else if (x>20&&x<=40) {
            y=10*0.1+10*0.075+(x-20)*0.05;
        }else if (x>40&&x<=60) {
            y=10*0.1+10*0.075+20*0.05+(x-40)*0.03;
        } else if (x>60&&x<=100) {
            y=10*0.1+10*0.075+20*0.05+20*0.03+(x-60)*0.015;
        }else if (x>100) {
            y=10*0.1+10*0.075+20*0.05+20*0.03+40*0.015+(x-100)*0.01;
        }
        System.out.println(y);
    }
}
```

【程序13】  
题目：一个整数，它加上100后是一个完全平方数，再加上168又是一个完全平方数，请问该数是多少？ 

```java
public class test13 {
    public static void main(String[] args) {
        for(int i=-100;i<10000;i++) {
            if(Math.sqrt(i+100)%1==0&&Math.sqrt(i+268)%1==0) {
                System.out.println(i);
            }
        }
    }
}
```

【程序14】 
题目：输入某年某月某日，判断这一天是这一年的第几天？  

```java
import java.util.*;
public class lianxi14 {
    public static void main(String[] args) {
        int year, month, day;
        int days = 0;
        int d = 0;
        int e;
        input fymd = new input();
        do {
            e = 0;
            System.out.print("输入年：");
            year =fymd.input();
            System.out.print("输入月：");
            month = fymd.input();
            System.out.print("输入天：");
            day = fymd.input();
            if (year < 0 || month < 0 || month > 12 ||day < 0 || day > 31) {
                System.out.println("输入错误，请重新输入！");
                e=1 ;
            }
        }while( e==1);
 
        for (int i=1; i <month; i++) {
            switch (i) {
                case 1:
                case 3:
                case 5:
                case 7:
                case 8:
                case 10:
                case 12:
                    days = 31;
                    break;
                case 4:
                case 6:
                case 9:
                case 11:
                    days = 30;
                    break;
                case 2:
                    if ((year % 400 == 0) || (year % 4 == 0&& year % 100 != 0)) {
                        days = 29;
                    } else {
                        days = 28;
                    }
                    break;
            }
            d += days;
        }
        System.out.println(year + "-" + month +"-" + day + "是这年的第" +(d+day) + "天。");
        }
    }
 
class input{
    public int input() {
        int value = 0;
        Scanner s = new Scanner(System.in);
        value = s.nextInt();
        return value;
    }
}
```

【程序15】  
题目：输入三个整数x,y,z，请把这三个数由小到大输出。  

```java
import java.util.Scanner;
public class test15 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int x=input.nextInt();
        int y=input.nextInt();
        int z=input.nextInt();
        int t=0;
        if(x>y) {
            t=x;
            x=y;
            y=t;
        }
        if(y>z) {
            t=z;
            z=y;
            y=t;
        }
        if(x>y) {
            t=x;
            x=y;
            y=t;
        }
        System.out.println(x+""+y+""+z);
    }
}
```

【程序16】
题目：输出9*9口诀。

```java
public class test16 {
    public static void main(String[] args) {
        for(int i=1;i<10;i++){
            for(int j=1;j<=i;j++) {
                System.out.print(i+"*"+j+"="+i*j);
                System.out.print(" ");
            }
        System.out.println("");
        }
    }
}
```

【程序17】  
题目：猴子吃桃问题：猴子第一天摘下若干个桃子，当即吃了一半，还不瘾，又多吃了一个   第二天早上又将剩下的桃子吃掉一半，又多吃了一个。以后每天早上都吃了前一天剩下   的一半零一个。到第10天早上想再吃时，见只剩下一个桃子了。求第一天共摘了多少。

```java
public class test17 {
    public static void main(String[] args) {
        int x=1;
        for(int i=10;i>1;i--) {
            x=(x+1)*2;
        }
        System.out.println(x);
    }
}
```

【程序18】  
题目：两个乒乓球队进行比赛，各出三人。甲队为a,b,c三人，乙队为x,y,z三人。已抽签决定比赛名单。有人向队员打听比赛的名单。a说他不和x比，c说他不和x,z比，请编程序找出三队赛手的名单。

```java
public class test18 {
    public static void main(String[] args) {
        for(char i='x';i<='z';i++) {
            for (char j='x';j<='z';j++) {
                if(i!=j) {
                    for(char k='x';k<='z';k++) {
                        if(i!=k&&j!=k) {
                            if(i!='x'&&j!='x'&&j!='z') {
                                System.out.println("a:"+i+"\nb:"+j+"\nc:"+k);
                            }
                        }
                    }
                }
            }
        }
    }
}
```

【程序19】  
题目：打印出如下图案（菱形）

  ![image-20241025121517261](https://bu.dusays.com/2026/01/02/69577f8ddc36d.png)

```java
public class lianxi19 {
    public static void main(String[] args) {
        int H = 7, W = 7;//高和宽必须是相等的奇数
        for(int i=0; i<(H+1) / 2; i++) {
            for(int j=0; j<W/2-i; j++) {
                System.out.print(" ");
            }
            for(int k=1; k<(i+1)*2; k++) {
                System.out.print('*');
            }
            System.out.println();
        }
        for(int i=1; i<=H/2; i++) {
            for(int j=1; j<=i; j++) {
                System.out.print(" ");
            }
            for(int k=1; k<=W-2*i; k++) {
                System.out.print('*');
            }
            System.out.println();
        }
    }
}
```

【程序20】  
题目：有一分数序列：2/1，3/2，5/3，8/5，13/8，21/13...求出这个数列的前20项之和。 

```java
public class test20 {
public static void main(String[] args) {
    double sum=0,ver=2;
        for(int i=1;i<=10;i++) {
            sum+=ver/i;
            ver+=i;
        }
    System.out.println(sum);
    }
}
```

【程序21】  
题目：求1+2!+3!+...+20!的和

```java
public class test21 {
    public static void main(String[] args) {
        long sum=0,ver=1;
        for(int i=1;i<=20;i++) {
            ver=ver*i;
            sum+=ver;
        }
    System.out.println(sum);
    }
}
```

【程序22】  
题目：利用递归方法求5!。

```java
public class test22 {
    public static void main(String[] args) {
        System.out.println(fac(5));
    }
    public static int fac(int i) {
        if(i==1){ 
            return 1;
        }else {
            return i*fac(i-1);
        }
    }
}
```

【程序23】  
题目：有5个人坐在一起，问第五个人多少岁？他说比第4个人大2岁。问第4个人岁数，他说比第3个人大2岁。问第三个人，又说比第2人大两岁。问第2个人，说比第一个人大两岁。最后问第一个人，他说是10岁。请问第五个人多大？  

```java
public class test23 {
    public static void main(String[] args) {
        int age=10;
        for(int i=2;i<=5;i++) {
            age+=2;
        }
        System.out.println( age);
    }
}
```

【程序24】  
题目：给一个不多于5位的正整数，要求：一、求它是几位数，二、逆序打印出各位数字。

```java
//使用了长整型最多输入18位
import java.util.Scanner;
public class test24 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        String toString=input.nextLine();
        char[] num=toString.toCharArray();
        System.out.println(num.length);
        for(int i=num.length;i>0;i--) {
            System.out.print(num[i-1]);
        }
    }
}
```

【程序25】  
题目：一个5位数，判断它是不是回文数。即12321是回文数，个位与万位相同，十位与千位相同。  

```java
import java.util.Scanner;
public class test25 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int numtest=input.nextInt();
        System.out.println(ver(numtest));
    }
 
    public static boolean ver(int num) {
        if(num<0||(num!=0&&num%10==0))
            return false;
        int ver=0;
        while(num>ver) {
            ver=ver*10+num%10;
            num=num/10;
        }
        return(num==ver||num==ver/10);
    }
}
```

【程序26】  
题目：请输入星期几的第一个字母来判断一下是星期几，如果第一个字母一样，则继续  判断第二个字母。  

```java
import java.util.*;
public class lianxi26 {
    public static void main(String[] args) {
        getChar tw = new getChar();
        System.out.println("请输入星期的第一个大写字母：");
        char ch = tw.getChar();
        switch(ch) {
            case 'M':
                System.out.println("Monday");
                break;
            case 'W':
                System.out.println("Wednesday");
                break;
            case 'F':
                System.out.println("Friday");
                break;
            case 'T': {
                System.out.println("请输入星期的第二个字母：");
                char ch2 = tw.getChar();
                if(ch2 == 'U'){System.out.println("Tuesday"); }
                else if(ch2 == 'H') {System.out.println("Thursday");}
                else {System.out.println("无此写法！");}
                };
                break;
            case 'S': {
                System.out.println("请输入星期的第二个字母：");
                char ch2 = tw.getChar();
                if(ch2 == 'U'){System.out.println("Sunday"); }
                else if(ch2 == 'A'){System.out.println("Saturday"); }
                else {System.out.println("无此写法！");}
                };
                break;
            default:System.out.println("无此写法！");
        }
    }
}
 
class getChar{
    public char getChar() {
        Scanner s = new Scanner(System.in);
        String str = s.nextLine();
        char ch = str.charAt(0);
        if(ch<'A' || ch>'Z') {
            System.out.println("输入错误，请重新输入");
            ch=getChar();
        }
        return ch;
    }
}  
```

【程序27】  
题目：求100之内的素数

```java
public class test27 {
    public static void main(String[] args) {
        System.out.println(2);
        boolean flag=true;
        for(int i=3;i<100;i+=2) {
            for(int j=2;j<=Math.sqrt(i);j++) {
                if(i%j==0) {
                    flag=false;
                    break;
                }else {
                    flag=true;
                }
        }
        if(flag==true)
            System.out.println(i);
        }
    }
}
```

【程序28】  
题目：对10个数进行排序  

```java
import java.util.Scanner;
public class test28 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int[] a=new int[10];
        for(int i=0;i<10;i++) {
            a[i]=input.nextInt();
        }
        int[] b=paixu(a);
        for(int i=0;i<b.length;i++) {
            System.out.println(b[i]);
        }
    }
 
    public static int[] paixu(int[] a) {
        for(int i=0;i<a.length;i++) {
            for(int j=i;j<a.length;j++) {
                if(a[i]>a[j]) {
                    int t=a[j];
                    a[j]=a[i];
                    a[i]=t;
                }
            }
        }
        return a;
    }
}
```

【程序29】  
题目：求一个3*3矩阵对角线元素之和 

```java
import java.util.Scanner;
public class test29 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int[][] a=new int[3][3];
        for(int i=0;i<3;i++) {
            for(int j=0;j<3;j++) {
                a[i][j]=input.nextInt();
            }
        }
        for(int i=0;i<3;i++) {
            for(int j=0;j<3;j++) {
                System.out.print(a[i][j]);
            }
            System.out.println("");
        }
        int sum=0;
        for(int i=0;i<3;i++) {
            for(int j=0;j<3;j++) {
                if(i==j) {
                    sum+=a[i][j];
                }
            }
        }
        System.out.println(sum);
    }
}
```

【程序30】  
题目：有一个已经排好序的数组。现输入一个数，要求按原来的规律将它插入数组中。

```java
import java.lang.reflect.Array;
import java.util.Arrays;
import java.util.Scanner;
public class test30 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int[] a=new int[10];
        for(int i=0;i<10;i++) {
            a[i]=input.nextInt();
        }
        Arrays.sort(a);
        for(int i=0;i<10;i++) {
            System.out.println(a[i]);
        }
        int x=input.nextInt();
        a=sort(a, x);
        for(int i=0;i<a.length;i++) {
            System.out.println(a[i]);
        }
    }
 
    public static int[] sort(int[] a,int b) {
        int[] c=new int[a.length+1];
        boolean flag=true;
        for(int i=0;i<a.length;i++) {
            if(flag) {
                if(a[i]<b) {
                    c[i]=a[i];
                }else {
                    c[i]=b;
                    flag=false;
                    System.arraycopy(a, i, c, i+1, a.length-i);
                }
            }else {
                break;
            }
        }
        return c;
    }
}
```

【程序31】
题目：将一个数组逆序输出。 

```java
import java.util.Scanner;
public class test31 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        int[] a=new int[20];
        int i=0;
        do {
            a[i]=input.nextInt();
            i++;
        }while(a[i-1]!=-1);
        for(int j=0;j<i-1;j++) {
            System.out.println(a[j]);
        }
        for(int k=i-1;k>0;k--) {
            System.out.println(a[k-1]);
        }
    }
}
```

【程序32】  
题目：取一个整数a从右端开始的4～7位。

```java
import java.util.Scanner;
public class test32 {
    public static void main(String[] args) {
        Scanner input=new Scanner(System.in);
        String toString=input.nextLine();
        char[] a=toString.toCharArray();
        int j=a.length;
        if(j<7) {
            System.out.println("error!");
        }
        System.out.println(a[j-7]+""+a[j-6]+""+a[j-5]+""+a[j-4]);
    }
}
```

【程序33】 
题目：打印出杨辉三角形（要求打印出10行如下图）   
      1  
     1  1  
    1  2  1  
   1  3  3  1  
  1  4  6  4  1  
1  5  10  10  5  1  
…………

```java
public class lianxi33 {
    public static void main(String[] args) {
        int[][] a = new int[10][10];
        for(int i=0; i<10; i++) {
            a[i][i] = 1;
            a[i][0] = 1;
        }
        for(int i=2; i<10; i++) {
            for(int j=1; j<i; j++) {
                a[i][j] = a[i-1][j-1] + a[i-1][j];
            }
        }
        for(int i=0; i<10; i++) {
            for(int k=0; k<2*(10-i)-1; k++) {
                System.out.print(" ");
            }
            for(int j=0; j<=i; j++) {
                System.out.print(a[i][j] + "   ");
            }
            System.out.println();
        }
    }
}
```

【程序34】  
题目：输入3个数a,b,c，按大小顺序输出。  

```java
import java.util.Scanner;
public class test34 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int a=input.nextInt();
        int b=input.nextInt();
        int c=input.nextInt();
        System.out.println(a+""+b+""+c);
        if(a>b) {
            int t=a;
            a=b;
            b=t;
        }
        if(b>c) {
            int t=b;
            b=c;
            c=t;
        }
        if(a>b) {
            int t=a;
            a=b;
            b=t;
        }
        System.out.println(a+""+b+""+c);
    }
}
```

【程序35】  

题目：输入数组，最大的与第一个元素交换，最小的与最后一个元素交换，输出数组。 

```java
import java.util.Arrays;
import java.util.Scanner;
public class test35 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int[] a=new int [5];
        for(int i=0;i<a.length;i++) {
            a[i]=input.nextInt();
        }
        for(int i=0;i<a.length;i++) {
            System.out.println(a[i]);
        }
        int maxi=0;
        int max=a[maxi];
        for(int i=1;i<a.length;i++) {
            if(max<a[i]) {
                max=a[i];
                maxi=i;
            }
        }
        int t=a[0];
        a[0]=a[maxi];
        a[maxi]=t;
        int mini=0;
        int min=a[mini];
        for(int i=1;i<a.length;i++) {
            if(min>a[i]) {
                min=a[i];
                mini=i;
            }
        }
        int k=a[a.length-1];
        a[a.length-1]=a[mini];
        a[mini]=k;
        for(int i=0;i<a.length;i++) {
            System.out.println(a[i]);
        }
    }
}
```

【程序36】  
题目：有n个整数，使其前面各数顺序向后移m个位置，最后m个数变成最前面的m个数  

```java
import java.util.Scanner;
public class test36 {
    public static void main(String[] args) {
        int N=10;
        int M=3;
        Scanner input =new Scanner(System.in);
        int[] a=new int[N];
        for(int i=0;i<N;i++) {
            a[i]=input.nextInt();
        }
        for (int i = 0; i < a.length; i++) {
            System.out.println(a[i]);
        }
        int[] b=new int[M];
        for(int i=0;i<M;i++) {
            b[i]=a[N-M+i];
        }
        for(int i=N-1;i>=M;i--) {
            a[i]=a[i-M];
        }
        for(int i=0;i<M;i++) {
            a[i]=b[i];
        }
        System.out.println("");
        for(int i=0;i<N;i++) {
            System.out.println(a[i]);
        }
    }
}
```

【程序37】  
题目：有n个人围成一圈，顺序排号。从第一个人开始报数（从1到3报数），凡报到3的人退出圈子，问最后留下的是原来第几号的那位。  

```java
import java.util.Scanner;
public class test37 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int n=input.nextInt();
        boolean[] arr=new boolean[n];
        for(int i=0;i<arr.length;i++) {
            arr[i]=true;
        }
        int leftCount=n;
        int index=0;
        int countNum=0;
        while(leftCount>1) {
            if(arr[index]==true) {
                countNum++;
                if(countNum==3) {
                    arr[index]=false;
                    leftCount--;
                    countNum=0;
                }
            }
            index++;
            if(index==n) {
                index=0;
            }
        }
        for(int i=0;i<n;i++) {
            if(arr[i]==true) {
                System.out.println(i+1);
            }
        }
    }
}
 
```

【程序38】  
题目：写一个函数，求一个字符串的长度，在main函数中输入字符串，并输出其长度。  

```java
import java.util.Scanner;
public class test38 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        String toString=input.nextLine();
        System.out.println(toString.length());
    }
}
```

【程序39】  
题目：编写一个函数，输入n为偶数时，调用函数求1/2+1/4+...+1/n,当输入n为奇数时，调用函数1/1+1/3+...+1/n(利用指针函数)  

```java
import java.util.Scanner;
public class test39 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        int n=input.nextInt();
        System.out.println(sum(n));
    }
    public static double sum(int n) {
        double sum=0;
        if(n%2==0) {
            for(int i=2;i<=n;i+=2) {
                sum+=(double)1/i;
            }
        }else {
            for(int i=1;i<=n;i+=2) {
                sum+=(double)1/i;
            }
        }
        return sum;
    }
}
```

【程序40】  
题目：字符串排序。 

```java
import java.util.Scanner;
public class test40 {
    public static void main(String[] args) {
        Scanner input =new Scanner(System.in);
        String[] toStrings=new String[5];
        String temp=null;
        toStrings[0]="afdfdcv";
        toStrings[1]="ghaf";
        toStrings[2]="fdasfas";
        toStrings[3]="tyrdfas";
        toStrings[4]="fadsfsd";
        for(int i=0;i<5;i++) {
            for(int j=i+1;j<5;j++) {
                if(!compare(toStrings[i], toStrings[j])) {
                    temp=toStrings[i];
                    toStrings[i]=toStrings[j];
                    toStrings[j]=temp;
                }
            }
        }
        for(int i=0;i<5;i++) {
            System.out.println(toStrings[i]);
        }
    }
 
    public static boolean compare(String s1,String s2) {
        boolean flag=true;
        for(int i=0;i<s1.length()&&i<s2.length();i++) {
            if(s1.charAt(i)>s2.charAt(i)) {
                    flag=false;
                    break;
            }else if (s1.charAt(i)<s2.charAt(i)) {
                    flag=true;
                    break;
            }else {
                if(s1.length()<s2.length()) {
                    flag=true;
                    break;
                }else {
                    flag=false;
                    break;
                }
            }
        }
        return flag;
    }
}
```

***\*【程序41\**】  
题目：海滩上有一堆桃子，五只猴子来分。第一只猴子把这堆桃子凭据分为五份，多了一个，这只猴子把多的一个扔入海中，拿走了**一份。第二只猴子把剩下的桃子又平均分成五份，又多了一个，它同样把多的一个扔入海中，拿走了一份，第三、第四、第五只猴子都是这样做的，问海滩上原来最少有多少个桃子？  

```java
public class lianxi41 {
    public static void main (String[] args) {
        int i,m,j=0,k,count;
        for(i=4;i<10000;i+=4) { 
            count=0;
            m=i;
            for(k=0;k<5;k++){
                j=i/4*5+1;
                i=j;
                if(j%4==0)
                    count++;
                else break;
           }
           i=m;
           if(count==4)
               {System.out.println("原有桃子 "+j+" 个");
           break;}
        }
    }
}
```

【程序42】  
题目：809*??=800*??+9*??+1  其中??代表的两位数,8*??的结果为两位数，9*??的结果为3位数。求??代表的两位数，及809*??后的结果。  

```java
//题目错了！809x=800x+9x+1 这样的方程无解。去掉那个1就有解了。
public class lianxi42 {
    public static void main (String[] args) {
        int a=809,b,i;
        for(i=10;i<13;i++)
            {b=i*a ;
            if(8*i<100&&9*i>=100)
                System.out.println("809*"+i+"="+"800*"+i+"+"+"9*"+i+"="+b);}
            }
}
```

【程序43】  
题目：求0—7所能组成的奇数个数。  
//组成1位数是4个。
//组成2位数是7*4个。
//组成3位数是7*8*4个。
//组成4位数是7*8*8*4个。
//......

```java
public class test43 {
    public static void main(String[] args) {
        int count=0;
        for(int i=1;i<76543210;i++) {
            if(i%2!=0) {
                count++;
            }
        }
        System.out.println(count);
    }
}
```

【程序44】  
题目：一个偶数总能表示为两个素数之和。  
//由于用除sqrt(n)的方法求出的素数不包括2和3，
//因此在判断是否是素数程序中人为添加了一个3。

```java
import java.util.*;
public class lianxi44 {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        int n,i;
        do{
            System.out.print("请输入一个大于等于6的偶数：");
            n = s.nextInt();
        } while(n<6||n%2!=0);   //判断输入是否是>=6偶数,不是,重新输入
        fun fc = new fun();
        for(i=2;i<=n/2;i++){
            if((fc.fun(i))==1&&(fc.fun(n-i)==1))
                {int j=n-i;
                 System.out.println(n+" = "+i+" +"+j);
             } //输出所有可能的素数对
       }
    }
}
 
class fun{
    public int fun (int a)    //判断是否是素数的函数
    {
        int i,flag=0;
        if(a==3){flag=1;return(flag);}
            for(i=2;i<=Math.sqrt(a);i++){
                if(a%i==0) {flag=0;break;}
            else flag=1;}
                return (flag) ;//不是素数,返回0,是素数,返回1
            }
        }
 
 
 
 
//解法二
import java.util.*;
public class lianxi44 {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        int n;
        do{
            System.out.print("请输入一个大于等于6的偶数：");
            n = s.nextInt();
        } while(n<6||n%2!=0);   //判断输入是否是>=6偶数,不是,重新输入
        for(int i=3;i<=n/2;i+=2){
            if(fun(i)&&fun(n-i)) {
                System.out.println(n+" = "+i+" +"+(n-i));
            } //输出所有可能的素数对
       }
    }
    static boolean fun (int a){    //判断是否是素数的函数
    boolean flag=false;
    if(a==3){flag=true;return(flag);}
        for(int i=2;i<=Math.sqrt(a);i++){
           if(a%i==0) {flag=false;break;}
           else flag=true;}
        return (flag) ;
    }
}
```

【程序45】  
题目：判断一个素数能被几个9整除  
//题目错了吧？能被9整除的就不是素数了！所以改成整数了。

```java
import java.util.*;
public class lianxi45 {
    public static void main (String[] args) {
        Scanner s = new Scanner(System.in);
        System.out.print("请输入一个整数：");
        int num = s.nextInt();
        int   tmp = num;
        int count = 0;
        for(int i = 0 ; tmp%9 == 0 ;){
            tmp = tmp/9;
            count ++;
        }
        System.out.println(num+" 能够被 "+count+" 个9整除。");
     }
}
```

【程序46】  
题目：两个字符串连接程序 

```java
import java.util.*;
public class lianxi46 {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        System.out.print("请输入一个字符串：");
        String str1 = s.nextLine();
        System.out.print("请再输入一个字符串：");
        String str2 = s.nextLine();
        String str = str1+str2;
        System.out.println("连接后的字符串是："+str);
    }
}
```

【程序47】  
题目：读取7个数（1—50）的整数值，每读取一个值，程序打印出该值个数的＊。  

```java
import java.util.*;
public class lianxi47 {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        int n=1,num;
        while(n<=7){
            do{
                System.out.print("请输入一个1--50之间的整数：");
                num=s.nextInt();
            }while(num<1||num>50);
            for(int i=1;i<=num;i++)
               {System.out.print("*");
            }
            System.out.println();
            n ++;
        }
    }
}
```

【程序48】  
题目：某个公司采用公用电话传递数据，数据是四位的整数，在传递过程中是加密的，加密规则如下：每位数字都加上5,然后用和除以10的余数代替该数字，再将第一位和第四位交换，第二位和第三位交换。  

```java
import java.util.*;
public class lianxi48   {
    public static void main(String args[]) {
        Scanner s = new Scanner(System.in);
        int num=0,temp;
        do{
            System.out.print("请输入一个4位正整数：");
            num = s.nextInt();
        }while (num<1000||num>9999);
        int a[]=new int[4];
        a[0] = num/1000; //取千位的数字
        a[1] = (num/100)%10; //取百位的数字
        a[2] = (num/10)%10; //取十位的数字
        a[3] = num%10; //取个位的数字
        for(int j=0;j<4;j++){
            a[j]+=5;
            a[j]%=10;
        }
        for(int j=0;j<=1;j++){
            temp = a[j];
            a[j] = a[3-j];
            a[3-j] =temp;
        }
        System.out.print("加密后的数字为：");
        for(int j=0;j<4;j++)
            System.out.print(a[j]);
        }
}
```

【程序49】  
题目：计算字符串中子串出现的次数 

```java
import java.util.*;
public class lianxi49 {
    public static void main(String args[]){
        Scanner s = new Scanner(System.in);
        System.out.print("请输入字符串：");
        String str1 = s.nextLine();
        System.out.print("请输入子串：");
        String str2 = s.nextLine();
        int count=0;
        if(str1.equals("")||str2.equals("")){
            System.out.println("你没有输入字符串或子串,无法比较!");
            System.exit(0);
        }else{
            for(int i=0;i<=str1.length()-str2.length();i++){
                if(str2.equals(str1.substring(i, str2.length()+i)))//这种比法有问题，会把"aaa"看成有2个"aa"子串。
                count++;
            }
            System.out.println("子串在字符串中出现: "+count+"次");
        }
    }
}
```

【程序50】  
题目：有五个学生，每个学生有3门课的成绩，从键盘输入以上数据（包括学生号，姓名，三门课成绩），计算出平均成绩，把原有的数据和计算出的平均分数存放在磁盘文件 "stud "中。

```java
import java.io.*;
import java.util.*;
public class lianxi50 {
    public static void main(String[] args){
        Scanner ss = new Scanner(System.in);
        String [][] a = new String[5][6];
        for(int i=1; i<6; i++) {
            System.out.print("请输入第"+i+"个学生的学号：");
            a[i-1][0] = ss.nextLine();
            System.out.print("请输入第"+i+"个学生的姓名：");
            a[i-1][1] = ss.nextLine();
        for(int j=1; j<4; j++) {
            System.out.print("请输入该学生的第"+j+"个成绩：");
            a[i-1][j+1] = ss.nextLine();
        }
        System.out.println("\n");
        }
        //以下计算平均分
        float avg;
        int sum;
        for(int i=0; i<5; i++) {
            sum=0;
        for(int j=2; j<5; j++) {
            sum=sum+ Integer.parseInt(a[i][j]);
        }
        avg= (float)sum/3;
        a[i][5]=String.valueOf(avg);
    }
        //以下写磁盘文件
        String s1;
        try {
            File f = new File("C:\\stud");
            if(f.exists()){
                System.out.println("文件存在");
            }else{
                System.out.println("文件不存在，正在创建文件");
                f.createNewFile();//不存在则创建
            }
        BufferedWriter output = new BufferedWriter(new FileWriter(f));
        for(int i=0; i<5; i++) {
            for(int j=0; j<6; j++) {
                s1=a[i][j]+"\r\n";
                output.write(s1);   
            }
        }
        output.close();
        System.out.println("数据已写入c盘文件stud中！");
       } catch (Exception e) {
          e.printStackTrace();
       }
    }
}
```

