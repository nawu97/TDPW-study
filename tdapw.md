# TDPW-study

参考：@https://tdap-help.github.io/TDAPW/
## TDPAW是基于QE（平面波）的含时第一性原理软件
## 一.理论
      对比TDAP
      @http://tdap.iphy.ac.cn/  ，TDPW是基于平面波基基底`{G}`来应用TDDFT,在平面波基底下，含时的Kohn-Sham(TDKS)态
      基本理论如下：
<img width="921" alt="捕获" src="https://user-images.githubusercontent.com/76439954/113078104-e9d6b000-917e-11eb-963e-6ca41d825ac4.PNG">
<img width="636" alt="2" src="https://user-images.githubusercontent.com/76439954/113078117-ee9b6400-917e-11eb-8504-26275d46ab7a.PNG">
<img width="598" alt="3" src="https://user-images.githubusercontent.com/76439954/113078122-f22eeb00-917e-11eb-8814-88f9ea7c69b0.PNG">

## 二.输入
### 1.参数表
<img width="772" alt="基本参数" src="https://user-images.githubusercontent.com/76439954/112947961-e93d0b80-90e3-11eb-8f3b-16320c21cb92.PNG">


### 2.更改占据数
```
cp pwscf.NAnorm.dat pwscf.TDPOP.in
```
找到要更改的第k个
然后执行 `tdpw.x pw.MoS2.scf.in`
#### 记得将`&CONTROL`里面的`tddft_is_on`设置成`true`

### 3.nstep的选择
#### 1）edt：默认10au，精确:20au (看模拟的现象有多长视情况而定) 1 a.u.=4.8378 * 10^-17 s
#### 2）nstep：不用过分考虑，TDAPW可以续算
##### A.如果是scf/nscf默认是1
##### B.如果是md默认是50
<img width="1054" alt="nstep" src="https://user-images.githubusercontent.com/76439954/112928592-60ae7300-90c3-11eb-9fe8-bee9c1309790.PNG">


#### 3）mstep：默认500,是将edt分成mstep份，按照每一份演化
#### 4）dt：(针对离子)固定原子情况下，dt=0；其他情况下，dt=edt

### 4.系综
NVT:如果只关心结构不关心时间演化，用NVT随便跑，跑的时间越长越好，采样间隔越大越好


NVE：NVT比NVE多了热浴的影响，采样更快，NVE跑出的构型不多，完全孤立，体系类似平衡位置振荡
## 三.输出
<img width="624" alt="输出文件" src="https://user-images.githubusercontent.com/76439954/113080298-26a4a600-9183-11eb-85e4-974564e79a89.PNG">
### 1. out文件如何查看
<img width="504" alt="1" src="https://user-images.githubusercontent.com/76439954/113156671-ab74db80-91e6-11eb-8a58-d93c939565e2.PNG">



