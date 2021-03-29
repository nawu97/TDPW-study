# TDPW-study @https://tdap-help.github.io/TDAPW/
## TDPAW是基于QE（平面波）的含时第一性原理软件
## 一.理论
      对比TDAP@http://tdap.iphy.ac.cn/  （基于原子轨道基），TDPW是基于平面波基基底`{G}`来应用TDDFT,在平面波基底下，含时的Kohn-Sham(TDKS)态
      <script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
      $$x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}$$\\(x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}\\)
## 二.输入
###1.
### 2.更改占据数
```
cp pwscf.NAnorm.dat pwscf.TDPOP.in
```
找到要更改的第k个
然后执行 `tdpw.x pw.MoS2.scf.in`
## 三.输出
