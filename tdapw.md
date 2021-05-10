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
<img width="772" alt="para" src="https://user-images.githubusercontent.com/76439954/117662646-5dce9400-b1d2-11eb-918a-3ad767b2bb8b.png">
<img width="585" alt="td_ht" src="https://user-images.githubusercontent.com/76439954/117662667-62934800-b1d2-11eb-95a6-35b26e60573d.png">
<img width="529" alt="td_ht1" src="https://user-images.githubusercontent.com/76439954/117662673-64f5a200-b1d2-11eb-9079-e428f8b729ab.png">




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
###  1. out文件如何查看
<img width="504" alt="1" src="https://user-images.githubusercontent.com/76439954/113156671-ab74db80-91e6-11eb-8a58-d93c939565e2.PNG">

## 四.注意事项
### 1.输出磁矩
在ecutrho=4*ecutwfc时(默认情况), 输出的pwscf.x.magKS.dat文件，每个能带满足 Sx^2+Sy^2+Sz^2 = 1
当ecutrho不等于4*ecutwfc时，之前tdpw.x输出的pwscf.x.magKS.dat文件，每个能带满足 Sx^2+Sy^2+Sz^2 = 1*c, c近似于(4*ecutwfc/ecutrho)^3
之前的计算结果可以通过人为归一化得到Sx^2+Sy^2+Sz^2 = 1
本邮件中的版本, ecutrho取任何值时, 每个能带均满足 Sx^2+Sy^2+Sz^2 = 1

bug原因: 密度和波函数的实空间网格点在ecutrho不等于4*ecutwfc时不相同，之前的程序采用实空间波函数计算 积分\inf psi(r)^*Spsi(r) dr时, 积分时dr用的密度网格点的dr. 

### 2.更正
仅增加在noncolinear时输出各个KS/TDKS波函数的自旋, 输出单位Bohr mag/cell, 乘上占据数和k点权重等于total magnetization.
默认输出td_outputS=T, 关闭输出设置td_outputS=F

输出文件格式同能级文件
```
pwscf.x.magKS.dat
pwscf.x.magTDKS.dat
pwscf.y.magKS.dat
pwscf.y.magTDKS.dat
pwscf.z.magKS.dat
pwscf.z.magTDKS.dat
```

### 3.新功能
新功能:    

        1. 新增加了一些参数: https://tdap-help.github.io/TDAPW/20-Input/2020-04-18-Parameter/        
        2. 默认不会输出向0时刻的基矢投影, 设置 cal_pop0 = T 进行计算      
        3. 指定DFT任意k点、能级的占据数: td_constrained = T;  TDDFT的初态占据数反转: pwscf.TDPOP.in或续算     
        4. 输出KS/TDKS波函数到pwscfN.save/pwscfNtdks.save: nwevc = N,  punchks/punchtdks = T, 可使用QE的后处理模块进行额外计算, i.e. wannier中心拟合      
        5. k点电流: td_current_k = T     
        6. 考虑tdks交叉项对电荷密度的贡献: use_tdks = T (同时建议设置td_ht=2 提高收敛性), (如果要得到震荡的Dipole信号此处要设为T)    
        7. 长度规范 tefield = T, GaugeField = F, 电场的空间分布: emaxpos, eopreg, 见QE    
        8. 速度规范(default) GaugeField = T   
        9. 进行BO计算/scf/relax/nscf/..., tddft_is_on = F   
        10. 在考虑SOC时输出电流 current_debug = T   
        11. 磁场 &SYSTEM  B_field(i)=0.1, i=1,3  /   
        12. 让程序保存数据正常结束计算:在运行目录创建TDPWSTOP文件; 让程序暂时休息不停止, 创建TDPWSLEEP, 删除TDPWSLEEP文件后，自动恢复计算   
        13. 续算, 前提是程序正常结束/创建TDPWSTOP让程序结束, 不要删除任何文件, 输入参数restart_mode = 'restart', 程序会续写投影等占据数文件.    
        14. 长度规范: theta(-t)电场: LastDiagonE, kick/delta电场: LastDiagonExp, 同时只设置TDEFIELD.in的第二行(如果设置第三行及以后,则会在模拟过程加光)   
        15. 速度规范: theta(-t)电场: LastDiagonA, 同时只设置TDEFIELD.in的第二行(如果设置第三行及以后,则会在模拟过程加光)    
        16. 计算Dipole td_outputD = T, 计算角动量td_outputL=T   
        17. Dipole/角动量的原点默认由emaxpos, eopreg定义, 也可以通过 use_origin    =   T, originx=0.5, originy=0.5, originz=0.5 指定   
        18. debug性质输出轨道角动量lorbm = T, 参数和计算模块同QE，计算的性质暂时不明朗    
        19. BUG修复: TDEFIELD.in 内容的行数不用设置和nstep一致, 缺少的行会自动补上0, 不再报错终止.   
        20. Image并行计算/RP-TDAP计算,复制tdpw.x为manytdpw.x, 脚本EXEC用manytdpw.x, 以文件名区分是否Image并行. k点并行支持同QE.   


### 4.常见问题
### 1）
问: 还有 use_origin 参数设置成 F 的含义是？
答: 默认计算时，光场的V=eEr, Dipole =\int rho(r)rdr, ....等F(r)的计算，需要定义原点，默认use_origin =F   
use_origin =F的含义是是使用QE的	emaxpos和eopreg定义原点和r的取值   
use_origin =T通过人为输入originx,originy,originz设置原点位置，分数坐标，范围【0-1】   
等价的：
使用  emaxpos = 0.95 !max position   
  eopreg = 0.1   !emaxpos+eopreg min position   
或者use_origin=T；originx=0.5   
originy=0.5   
originz=0.5   
![微信图片_20210420161914](https://user-images.githubusercontent.com/76439954/115362491-2af93780-a1f4-11eb-9fea-f4e42dc13c1e.jpg)
### 2）
QE的pp.x可以直接画各个轨道的自旋空间分布，使用：
```
nwevc        =  N
punchks       =  F
punchtdks     =  F
```
输出各个时刻的ks/tdks即可以自旋的空间分布，积分就是mag.EIG文件

<img width="721" alt="微信图片_20210420162054" src="https://user-images.githubusercontent.com/76439954/115362715-67c52e80-a1f4-11eb-8bd0-008b48d657f3.png">


### 3)
TDPW模拟过程中dt的设置
1. 如果不设置dt，默认dt=20au，显然不适合跑tddft   
2. 设置dt=edt是电子原子一起演化，即Ehrenfest计算   
3. 设置dt=1E-15或0.0，为固定原子位置，只演化电子坐标，为TDDFT计算   


