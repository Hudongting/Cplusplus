# DM-TEST
## 变形镜的静态性能测试
### 静态性能测试光路图
### 测驱动器响应函数
- 所测为zernike系数相关的响应函数<br>
- 变形镜表面不平整，直接响应函数误差较大,因此我们可以先测
+V，-V下的面型，通过两者相减来消除误差<br>
- 电压变化过大的时候，哈特曼出现测不准的现象，所以不能使用单
位电压进行测量，我们选择了0.3进行实验，之后再做归一化处理<br>
### 数据处理
- 元数据：  
  "\DM_TEST\data\\.cav"  
  (2n-1).csv ，(2n).csv 分别表示给第n个驱动器施加0.3正电压和负电压的哈特曼数据  
  提取36阶zernike系数
- 方法：
- 代码  
  ```matlab
  % 计算响应函数
  clear
  clc
  %% 常参
  Nzer = 36;  %zernike项数
  Nact = 69;  %驱动器数
  bNor = 1;   %Zernike归一化因子，0：归一化因子为1；1：归一化因子按正常算
  % 坐标划分
  N = 512;
  x = linspace(-1,1,N);
  y = linspace(-1,1,N);
  [X,Y] = meshgrid(x,y);
  WF = zeros(N,N);
  WWF = zeros(N*9,N*9);
  % 定义mask
  mask = sqrt(X.*X+Y.*Y)<=1;

  cd('data')
  %% 获取响应函数coe
  % 元数据地址 \data
  coe = zeros(Nact,Nzer);
  % for n = 1:Nact
  ha = tight_subplot(9,9,[0,0],[0,0],[0,0]);
  for n = 1:Nact
    %% 获取响应函数
    %确定对应文件名
    filename1 = [num2str(2*n-1),'.csv'];
    filename2 = [num2str(2*n),'.csv'];
    %读取zernike系数coe(n)
    coe1 = xlsread(filename1,'D45:D80');
    coe2 = xlsread(filename2,'D45:D80');
    coe(n,:) = (coe1-coe2)/0.6;
    clear coe1 coe2
    
    %% 画出69个驱动器响应图形
    %根据coe计算波前
    WF = zeros(N,N);
    for i = 1:Nzer
        WF = WF + coe(n,i)*cal_zernike(X,Y,N,i,mask,bNor);
    end
    WF= WF.*mask;
    %     [3:1:7 11:1:17 19:1:63 65:1:71 75:1:79];
    if n < 6
        axes(ha(n+2));
        surf(WF);view(0,90);shading flat;axis off;axis square;axis([1 N 1 N]);set(gca,'XTick',[1 128 256 384 512]);set(gca,'YTick',[1 128 256 384 512]);
    elseif n < 13
        axes(ha(n+5));
        surf(WF);view(90,90);shading flat;axis off;axis square;axis([1 N 1 N]);
    elseif n < 58
        axes(ha(n+6));
        surf(WF);view(90,90);shading flat;axis off;axis square;axis([1 N 1 N]);
    elseif n < 65
        axes(ha(n+7));
        surf(WF);view(90,90);shading flat;axis off;axis square;axis([1 N 1 N]);
    else
        axes(ha(n+10));
        surf(WF);view(90,90);shading flat;axis off;axis square;axis([1 N 1 N]);
    end
  end
  ```
<img src=".\fig\1.png" width="500" hegiht="500"  align=center />

### 控制变形镜
