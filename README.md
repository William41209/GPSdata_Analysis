# GPS 軌跡資料分析

## 1. 資料描述(data description)

此資料是關於一車輛於高速公路行駛的GPS點位，記錄了當下的經度(lon)、緯度(lat)、高度(ele)、時間等數據。

其中，我們將取用中段的資料進行測試，如下圖。

![DataDes](https://i.meee.com.tw/mT7TNBy.png "datades")

## 2. 地圖化(data to map)

可以看到實際放入地圖的藍點，如下圖。

![Graphic](https://i.meee.com.tw/oA6PI3S.png "graphic")

## 3. 資料清理與飄移值檢查(data cleaning & noise detecting)

想法如下：

記錄點與點之間的 **距離** 為 **(經度差平方+緯度差平方)開根號** 。

再計算點與點之間的 **時間差** 。

最終紀錄下 **速率** 為 **距離/時間差** 。

繪製速率的 **盒狀圖(box plot)** 檢查是否有**飄移值**出現。

若有 → 去除它並再次檢查。

若無 → 執行下一步。

此為最終速率的盒狀圖，並無飄移值的產生。

![Boxplot](https://i.meee.com.tw/vVpAMni.png "boxplot")

## 4. 模型的架設(model construction)

這邊使用了兩個不同的模型去建構，也發現到合適與不合適的地方。

### 4.1. ) 廣義線性模型(GLM)

廣義線性模型為優化版的線性模型，彌補了線性模型僅能為直線的缺點，可以更擬和貼近資料點並為曲線的模式。

#### 4.1.1 ) 直接套用

如下圖，可以發現非常不吻合，其原因是使用最小平方法，與各點之間最短距離仍以直線最佳。

**藍線**：原資料點路徑

**綠線**：`glm`直接套用路徑

![GLM1](https://i.meee.com.tw/wKnewt3.png "glm1")

#### 4.1.2 ) 局部 GLM 

若改以分段式 `glm` ，如下圖。

**以每 20 個點分段**：

![GLMp1](https://i.meee.com.tw/Rk5VX1u.png "glmp1")

**以每 10 個點分段**：

![GLMp2](https://i.meee.com.tw/W57VeJh.png "glmp2")

**以每 5 個點分段**：

![GLMp3](https://i.meee.com.tw/fNIdBih.png "glmp3")

可以發現到以 5 個點分段的擬和效果是最好的。

### 4.2. ) 區域性迴歸模型(LOESS)

可以發現分段式做迴歸式較好的，但路徑仍非常的有稜有角，不夠平滑。

因此我們這裡採用 `LOESS` 的方式來平滑化此路徑，並比較與分段式 `glm` 的成果。

**藍線**：原資料點路徑

**紅線**：`LOESS`直接套用路徑

![LOESS](https://i.meee.com.tw/pfwyjdK.png "loess")

由此我們可以發現使用 `LOESS` 能比分段式 `glm` 來的更加平滑，

但分段式 `glm` 卻比 `LOESS` 更為擬和原資料點。

故兩方法各有優缺點，都是屬於可以執行的模型。

## 5. 地圖上兩方法的比較(Two model using in map)

如下圖，我們針對特定路段區看看。

### 5.1. ) 分段式 glm

![pcGLM](https://i.meee.com.tw/dRZTAlT.png "pcglm")

### 5.2. ) 區域性迴歸模型

![pcLOESS](https://i.meee.com.tw/cxjSBfG.png "pcloess")
