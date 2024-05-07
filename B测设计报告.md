# B测设计报告

### 1. QAM原理

正交振幅调制（QAM）是用两个独立的基带数字信号对两个互相正交的同频载波进行抑制在播的双边带调制，利用这种已调信号在同一带宽内频谱正交的性质来实现两路并行的数字信息传输。它是把MASK与MPSK两种结合到一起的调制技术，使得带宽得到双倍拓展。

**正交振幅调制（QAM）是一种振幅和相位联合键控**

![图片2](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%872.png)

第K个带通信号码元波形公式：

>$$
>s_k(t) = A_k\cos{(\omega_0t+\theta_k)}kT<t\leq(k+1)T
>$$
>
>其中$A_k$为振幅调制，$\theta_k$为相位调制

进一步进行展开

>$$
>\begin{aligned}
>s_k(t) &= A_k\cos{(\omega_0t+\theta_k)}kT\\
>	   &=A_k\cos{\theta_k}\cos{\omega_0t} - A_k\sin{\theta_k}\sin{\omega_0t}\\
>\end{aligned}
>$$
>
>$$
>令X_k=A_kcos\theta_k,Y_k=-A_ksin\theta_k，则信号变为：\\
>s_k(t)=X_kcos\omega_0t+Y_ksin\omega_0t
>$$
>
>查看展开后的公式可以发现，一个QAM信号的码元波形，可以看作是两个载波正交的振幅键控信号叠加而成。由于cost和sint是正交的，所以这是两路正交信号的叠加。

所以，如果我们想要的到一个QAM信号的码元波形，就要分别得到I路的幅度值，和Q路的幅度值。这里以16QAM为例进行说明。由于16QAM是16进制的，每4个比特为一组对应一个带通信号的码元波形。 对于给定的基带信号的比特流，就要每4个为一组，每一组绘制出对应的带通信号的码元波形。

基带信号的4个比特，有16种不同的比特组合。![图片4](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%874.png)

要进行仿真就要给出这16种比特组合中，每种比特组合对应的带通信号的码元波形是多少。要想得到码元波形，就要知道两个载波正交的振幅键控信号的幅度值分别是多少。

![图片5](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%875.png)

这种复杂的对应关系可以展示在一个仿真星座图中：

![图片6](https://gitee.com/xiaofeifei12138/picture/raw/master/img/%E5%9B%BE%E7%89%876.png)

仿真星座图：

![image-20230508150106221](C:\Users\盖乐\Desktop\b测\B测_QAM调制解调的仿真实现\b测报告.assets\8@R021@Y`Z77IY`MWE5Y5NO.png)

### 2.详细步骤

>QAM的调制与解调过程
>
>* 1.发射端(二进制序列${a_k}$进入,其速率${R_b=\frac{1}{T_b}}$。,经过串并变换，$a_{2n}$进I路、${a_{2n+1}}$进Q路，并且二者的速率变为原来的一半$R_b/2$，二者分别通过电平转换把二进制变换为$\sqrt{M}$进制，基带成型I路:$I(t)*cos(\omega _ct)$、Q 路:Q(t)*(-sin(ωt))，二者相加，得到矩形星座 MQAM信号*
>
>MOAM信号=$I(t)*\cos{(\omega_ct)}-Q(t)*\sin{(\omega_ct)}$。
>
>![image-20230508151012520](https://gitee.com/xiaofeifei12138/picture/raw/master/img/image-20230508151012520.png)
>
>* 2.接收端
>
> ![image-20230508151052059](https://gitee.com/xiaofeifei12138/picture/raw/master/img/image-20230508151052059.png)
>
> 本次仿真采用星座点图，等效基带的形式，发射端(二进制序列${a_k}$进入，其速率$\R_b=1/T_b$，经过串并变换，${a_{2n}}$进I路、${a_{2n+1}}$进Q路，并且二者的速率变为原来的一半$R_b/2$，均转换为星座点图 $a+bj$ 复数形式，其中a代表I路、b代表 Q路，通过载波加噪声;中间过程简化，接收端收到可变为$r_1+r_2j$复数形式的判决。

