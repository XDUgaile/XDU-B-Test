# B测报告

## 一、题目要求

> 任务：使用MATLAB软件，实现对QAM系统调制与解调过程的仿真，然后分析系统的可靠性。

（1）对原始信号分别进行4QAM和16QAM调制，画出星座图；

（2）采用高斯白噪声信道传输信号，画出信噪比为15dB时，4QAM和16QAM的接收信号星座图；

（3）画出两种调制方式的眼图；

（4）解调接收信号，分别绘制4QAM和16QAM的误码率曲线图，并与理论值进行对比；

## 二、原理分析

### 1. QAM原理

正交振幅调制（QAM）是用两个独立的基带数字信号对两个互相正交的同频载波进行抑制在播的双边带调制，利用这种已调信号在同一带宽内频谱正交的性质来实现两路并行的数字信息传输。它是把MASK与MPSK两种结合到一起的调制技术，使得带宽得到双倍拓展。

**正交振幅调制（QAM）是一种振幅和相位联合键控**

![图片2](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%872.png)

第K个带通信号码元波形公式：
>$$
>s_k(t) = A_k\cos{(\omega_0t+\theta_k)}kT<t\leq(k+1)T
>$$
其中$A_k$为振幅调制，$\theta_k$为相位调制

进一步进行展开
>$$
>\begin{aligned}
>s_k(t) &= A_k\cos{(\omega_0t+\theta_k)}kT\\
>	   &=A_k\cos{\theta_k}\cos{\omega_0t} - A_k\sin{\theta_k}\sin{\omega_0t}\\
>\end{aligned}
>$$
>$$
>令X_k=A_kcos\theta_k,Y_k=-A_ksin\theta_k，则信号变为：\\
>s_k(t)=X_kcos\omega_0t+Y_ksin\omega_0t
>$$
>
>查看展开后的公式可以发现，一个QAM信号的码元波形，可以看作是两个载波正交的振幅键控信号叠加而成。由于cost和sint是正交的，所以这是两路正交信号的叠加。

![[公式]](https://www.zhihu.com/equation?tex=X_k)和![[公式]](https://www.zhihu.com/equation?tex=Y_k)是由振幅和相位决定的取离散值的变量。每一个码元可以看作是两个载波正交的振幅键控信号之和。这里以16QAM为例进行说明。由于16QAM是16进制的，每4个比特为一组对应一个带通信号的码元波形。 对于给定的基带信号的比特流，就要每4个为一组，每一组绘制出对应的带通信号的码元波形。

- **原理图（以16QAM为例）**

![img](https://pic3.zhimg.com/80/v2-d6549da687d3cf9feadf7f1126fc0a96_1440w.jpg)

![img](https://pic3.zhimg.com/80/v2-14d3806c0e54ec8dccfcb52c69a5e8da_1440w.jpg)

基带信号的4个比特，有16种不同的比特组合。![图片4](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%874.png)

要进行仿真就要给出这16种比特组合中，每种比特组合对应的带通信号的码元波形是多少。要想得到码元波形，就要知道两个载波正交的振幅键控信号的幅度值分别是多少。

![图片5](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%875.png)

这种复杂的对应关系可以展示在一个仿真星座图中：

![image-20230508150106221](C:\Users\盖乐\Desktop\b测\B测_QAM调制解调的仿真实现\b测报告.assets\8@R021@Y`Z77IY`MWE5Y5NO.png)

## 三、需求方案

 （1）对原始信号分别进行4QAM和16QAM调制，画出星座图；

分析：

原始信号：我们创建10000*log2(QAM)个随机信号（0  or  1）将其转换为（0---(QAM-1)）的值

​					eg：01  --->  1  10--->2

​							0100-->4   1111-->15

```matlab
M = input('输入qam');
m=log2(M);
n=10000; %Random number.
x1=randi([0,1],m*n,1); %random bit stream.

[m1,n1] = size(x1);
fprintf('原始信号1 %d行%d列  最小值%d 最大值%d\n',m1,n1,min(x1),max(x1));
x2=reshape(x1,m,length(x1)/m);
[m2,n2] = size(x2);
fprintf('原始信号2 %d行%d列\n',m2,n2);
x3=bi2de(x2','left-msb');%将二进制随机序列转换为M进制序列
[m3,n3] = size(x3);
fprintf('原始信号3 %d行%d列  最小值%d 最大值%d 平均值%f\n',m3,n3,min(x3),max(x3),mean(x3));
```

| 4QAM                                              | 16QAM |
| ------------------------------------------------- | ----- |
| ![img](b测报告.assets/CTU`XU78R4PYN[[_6GI]KL.png) |       |

QAM调制:使用qammod函数，调制根据M确定

```matlab
%（1）对原始信号分别进行4QAM和16QAM调制，画出星座图
fprintf('%dQAM调制 可视化星座图\n',M);
y=qammod(x1,M,'bin','InputType','bit');
```

星座图

```matlab
scatterplot(y);%MQAM星座图
text(real(y)+0.1,imag(y),dec2bin(x3));
hold on
```

> （2）采用高斯白噪声信道传输信号，画出信噪比为15dB时，4QAM和16QAM的接收信号星座图；

添加信噪比15db的噪声：使用awgn函数

```matlab
y_noisy=awgn(y,15);
```

添加噪声的接收信号图

```matlab
h=scatterplot(y_noisy,1,0,'b.');
hold on;
scatterplot(y,1,0,'r*',h);
title('接收信号星座图');
legend('含噪声接收信号','不含噪声信号');
hold on;
```

 （3）画出两种调制方式的眼图

使用eyediagram函数

```matlab
eyediagram(y,2);
eyediagram(y_noisy,2);
```

 （4）解调接收信号，分别绘制4QAM和16QAM的误码率曲线图，并与理论值进行对比；

解调信号：使用qamdemod函数

```matlab
y_deqam1=qamdemod(y,M,'bin','OutputType','bit');%解调信号
figure
subplot(2,1,1);
stem(x1(1:100),'filled');            %基带信号前100位
title('基带二进制随机序列');
xlabel('比特序列');ylabel('信号幅度');
subplot(2,1,2);
stem(y_deqam1(1:100));                  %画出相应的经过QAM调制解调后的信号的前100位
title('解调后的二进制序列');
xlabel('比特序列');ylabel('信号幅度');
```

| 4QAM                                               | 16QAM |
| -------------------------------------------------- | ----- |
| ![img](b测报告.assets/85HBOMQ@X34Z[OS6`NC3V5I.png) |       |

分析：可以看出解调后出错的比特很少。

理论误码率：使用berawgn函数，  format long可以提高数据精度

```matlab
SNR_in_dB1=0:1:24;
format long;
berQ = berawgn(SNR_in_dB1,'qam',M);
```

实际误码率:查看每个信噪比的噪音下，解调后的比特码流与初始的差别

```matlab
NR_in_dB=0:1:24; %AWGN信道信噪比;
for j=1:length(SNR_in_dB)
    format long;
    y_noise = awgn(y,SNR_in_dB(j));%加入不同强度的高斯白噪声;
    y_output = qamdemod(y_noise,M,'bin','OutputType','bit'); %对己调信号进行解调
    num=0;
    for i=1:length(x1)
        if (y_output(i)~=x1(i))
            num=num+1;
        end
    end

    [number, ratio] = biterr(x1,y_output);
    Pe0(j) = num/length(y_output);
    Pe(j) = number/length(y_output); 
end

```

(实际误码率)使用了for循环寻找和biterr查找两种方法对比，结果如下：

| 4QAM                                     | 16QAM                                      |
| ---------------------------------------- | ------------------------------------------ |
| ![C](b测报告.assets/C-1620649590607.png) | ![C1](b测报告.assets/C1-1620649593944.png) |

误码曲线图：

```matlab
igure();
semilogy(SNR_in_dB,Pe,'green*-');
hold on;
semilogy(SNR_in_dB1,berQ);
title('误码率比较');
legend('实际误码率','理论误码率');
hold on;
grid on;
```

## 四、仿真结果及结论

### 1. 进行4QAM和16QAM调制（星座图）

画星座图：

4-QAM星座图：

![2023-05-08_152306](https://gitee.com/xiaofeifei12138/picture/raw/master/img/2023-05-08_152306.png)

16-QAM的星座图

![2](https://gitee.com/xiaofeifei12138/picture/raw/master/img/2.png)

### 2. 信噪比为15dB时，4QAM和16QAM的接收信号星座图

>```matlab
>nsymbol=10000;%选10000个点
>EsN0=0:15;%信噪比范围
>snr1=10.^(EsN0/10);%将db转换为线性值
>msg=randi([0,M-1],1,nsymbol);%0到15之间随机产生一个数,数的个数为：1乘nsymbol，得到原始数据
>msg1=graycode(msg+1);%对数据进行格雷映射
>```

4QAM加噪声后图像

![3](https://gitee.com/xiaofeifei12138/picture/raw/master/img/3.png)

16QAM加噪声后图像

![4](C:\Users\盖乐\Desktop\b测\b测\4.png)

### 3. 画出两种调制方式的眼图

4-QAM与16QAM的眼图：

![9](https://gitee.com/xiaofeifei12138/picture/raw/master/img/9.png)

![7](https://gitee.com/xiaofeifei12138/picture/raw/master/img/7.png)

加入噪声后的眼图

![10](https://gitee.com/xiaofeifei12138/picture/raw/master/img/10.png)

![8](https://gitee.com/xiaofeifei12138/picture/raw/master/img/8.png)

### 4.分别绘制4QAM和16QAM的误码率曲线图，并与理论值进行对比

**结论：**由图可知，16QAM的误符号率和仿真理论误符号率完全拟合，16QAM的误比特率在性躁比越来越高情况下拟合情况和仿真理论误比特率越来越接近。

![4532345](https://gitee.com/xiaofeifei12138/picture/raw/master/img/4532345.png)

![6](https://gitee.com/xiaofeifei12138/picture/raw/master/img/6.png)


##  五、仿真代码

>```matlab
>clc;clear all;close all;
>nsymbol=10000;%表示一共有多少个符号，这里定义100000个符号
>M=4;
>N=16;
>graycode=[0 1 2 3];%格雷映射编码规则
>graycode1=[0 1 3 2 4 5 7 6 12 13 15 14 8 9 11 10];%格雷映射十进制的表示
>EsN0=0:15;%信噪比范围
>snr=15;
>snr1=10.^(EsN0/10);%将db转换为线性值
>msg=randi([0,M-1],1,nsymbol);
>msg1=graycode(msg+1);
>msgmod=qammod(msg1,M);%调用matlab中的qammod函数，16QAM调制方式的调用(输入0到15的数，M表示QAM调制的阶数)得到调制后符号
>eyediagram(msgmod,2)%4眼图
>
>
>scatterplot(msgmod);%调用matlab中的scatterplot函数,画星座点图
>spow=norm(msgmod).^2/nsymbol;%取a+bj的模.^2得到功率除整个符号得到每个符号的平均功率
>%16QAM
>nsg=randi([0,N-1],1,nsymbol);
>nsg1=graycode1(nsg+1);
>nsgmod=qammod(nsg1,N);
>scatterplot(nsgmod);%调用matlab中的scatterplot函数,画星座点图
>spow1=norm(nsgmod).^2/nsymbol;
>eyediagram(nsgmod,2)%16眼图
>
>for i=1:length(EsN0)
>   sigma=sqrt(spow/(2*snr1(i)));%4QAM根据符号功率求出噪声的功率
>   sigma1=sqrt(spow1/(2*snr1(i)));%16QAM根据符号功率求出噪声的功率
>  rx=msgmod+sigma*(randn(1,length(msgmod))+1i*randn(1,length(msgmod)));%4QAM混入高斯加性白噪声
>  rx1=nsgmod+sigma*(randn(1,length(nsgmod))+1i*randn(1,length(nsgmod)));%16QAM混入高斯加性白噪声
>  y=qamdemod(rx,M);%4QAM的解调
> y1=qamdemod(rx1,N);%16QAM的解调
> decmsg=graycode(y+1);%4QAM接收端格雷逆映射，返回译码出来的信息，十进制
> decnsg=graycode1(y1+1);%16QAM接收端格雷逆映射
> [err1,ber(i)]=biterr(msg,decmsg,log2(M));%一个符号四个比特，比较发送端信号msg和解调信号decmsg转换为二进制，ber(i)错误的比特率
>[err2,ser(i)]=symerr(msg,decmsg);%4QAM求实际误码率
>[err1,ber1(i)]=biterr(nsg,decnsg,log2(N));
>[err2,ser1(i)]=symerr(nsg,decnsg);%16QAM求实际误码率
>end
>
>
>%4QAM
>signal_time_C_W_R=awgn(real(msgmod),snr);
>signal_time_C_W_i=awgn(imag(msgmod),snr);
>signal_time_C_W=complex(signal_time_C_W_R,signal_time_C_W_i);
>scatterplot(rx);%调用matlab中的scatterplot函数,画rx星座点图
>p = 2*(1-1/sqrt(M))*qfunc(sqrt(3*snr1/(M-1)));
>ser_theory=1-(1-p).^2;%4QAM理论误码率
>ber_theory=1/log2(M)*ser_theory;
>eyediagram(signal_time_C_W,2)%4眼图噪声
>%16QAM
>signal_time_C_W_R_1=awgn(real(nsgmod),snr);
>signal_time_C_W_i_1=awgn(imag(nsgmod),snr);
>signal_time_C_W_1=complex(signal_time_C_W_R_1,signal_time_C_W_i_1);
>scatterplot(rx1);
>p1=2*(1-1/sqrt(N))*qfunc(sqrt(3*snr1/(N-1)));
>ser1_theory=1-(1-p1).^2;%16QAM理论误码率
>ber1_theory=1/log2(N)*ser1_theory;%得到误比特率
>eyediagram(signal_time_C_W_1,2)%16眼图噪声
>%绘图
>figure()
>semilogy(EsN0,ber,"o", EsN0, ser, "*",EsN0, ser_theory, "-", EsN0, ber_theory, "-");
>title("4-QAM载波调制信号在AWGN信道下的误比特率性能")
>xlabel("EsN0");
>ylabel("误比特率和误符号率");
>legend("误比特率", "误符号率","理论误符号率","理论误比特率");
>%阶数不同,4和16QAM调制信号在AWGN信道的性能比较
>figure()
>semilogy(EsN0,ser_theory,'o',EsN0,ser1_theory,'o');%ber ser比特仿真值 ser1理论误码率 ber1理论误比特率
>title('4和16QAM调制信号在AWGN信道的性能比较');grid;
>xlabel('Es/N0(dB)');%性躁比
>ylabel('误码率');%误码率
>legend('4QAM理论误码率','16QAM理论误码率');
>```
>

