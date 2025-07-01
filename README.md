# PUBG快速游戏识别系统说明

这上面文件太大了 提供一个蓝奏云的  如果有谁可以做出更优化版本的 欢迎提交哦  目前市面上卖的都是这种 我这个只是粗板的 很多细节都要完善 有意思可以多点一下

下载文件夹
https://wwsm.lanzouo.com/b0nzapn1i 密码:xswl
单个文件下载 
PY.zip https://wwsm.lanzouo.com/iMB0O300bvhi 
罗技驱动安装包.exe https://wwsm.lanzouo.com/iveVZ300bwwj 
Python代码.zip https://wwsm.lanzouo.com/i603d300c0sj 


如果你的分辨率不是1080*1920的 可以用微信工具 一点点定位坐标哦  只要按照1920*1080的文件格式来即可 

[global]
debug = false

[1920_1080]
# 注意：如果截图的宽高是(1080, 1920)，说明屏幕是竖向的
# 修正坐标系适配竖屏布局
bag_icon = 665,880,60,60 # 背包
weapon1 = 1374,94,72,28  # 武器1
weapon1_grip = 1445,255,20,20 # 武器1握把
weapon1_muzzle = 1358,240,20,20  # 武器1枪口
weapon1_stock = 1765,260,20,20  # 武器1屁股
weapon2 = 1374,320,72,28  # 武器2
weapon2_grip = 1455,470,20,20  # 武器2握把
weapon2_muzzle = 1358,464,20,20  # 武器2枪口
weapon2_stock = 1765,480,20,20    # 武器2屁股
# 增加更多识别区域
weapon1_scope = 1612,125,36,24  # 武器1瞄具
weapon2_scope = 1612,350,36,24 # 武器2瞄具

[2560_1440]
bag_icon = 920,1200,80,80
weapon1 = 1600,600,400,120
weapon2 = 1600,900,400,120

[3840_2160]
bag_icon = 1800,2000,120,120
weapon1 = 3200,1200,600,200
weapon2 = 3200,1800,600,200 


## 解决的问题

### 1. 识别速度慢的问题
**原问题**：识别到背包后，后续识别特别慢

**解决方案**：
- **多线程并行处理**：使用 `ThreadPoolExecutor` 同时处理多个模板匹配
- **减少尺度数量**：从原来的多个尺度减少到 3 个关键尺度 (0.9, 1.0, 1.1)
- **优化检测流程**：减少不必要的计算步骤
- **冷却时间优化**：将检测冷却时间从 1.0 秒减少到 0.5 秒

### 2. 不同背景下识别不出来的问题
**原问题**：在不同背景下识别不出来

**解决方案**：
- **二值化处理**：使用 `cv2.THRESH_BINARY + cv2.THRESH_OTSU` 自动二值化
- **轮廓匹配**：只判断形状，不依赖颜色和纹理
- **形态学去噪**：使用开运算去除小噪点
- **形状特征提取**：提取面积、周长、长宽比等形状特征

## 技术改进

### 1. 图像预处理
```python
def preprocess_image(self, image: np.ndarray) -> np.ndarray:
    # 转换为灰度图
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    
    # 自动二值化
    _, binary = cv2.threshold(gray, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
    
    # 形态学去噪
    kernel = np.ones((2, 2), np.uint8)
    binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN, kernel)
    
    return binary
```

### 2. 轮廓特征提取
```python
def extract_contour_features(self, binary_image: np.ndarray) -> Dict:
    # 提取轮廓
    contours, _ = cv2.findContours(binary_image, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
    
    # 找到最大轮廓
    main_contour = max(contours, key=cv2.contourArea)
    
    # 计算形状特征
    area = cv2.contourArea(main_contour)
    perimeter = cv2.arcLength(main_contour, True)
    x, y, w, h = cv2.boundingRect(main_contour)
    aspect_ratio = w / h if h > 0 else 0
    
    return {
        'contour': main_contour,
        'area': area,
        'perimeter': perimeter,
        'bbox': (x, y, w, h),
        'aspect_ratio': aspect_ratio
    }
```

### 3. 相似度计算
```python
def calculate_similarity(self, features1: Dict, features2: Dict) -> float:
    # 轮廓形状相似度 (60%)
    shape_similarity = cv2.matchShapes(features1['contour'], features2['contour'], cv2.CONTOURS_MATCH_I1, 0)
    shape_score = max(0, 1 - shape_similarity)
    
    # 面积比例相似度 (30%)
    area_ratio = min(features1['area'], features2['area']) / max(features1['area'], features2['area'])
    
    # 长宽比相似度 (10%)
    aspect_diff = abs(features1['aspect_ratio'] - features2['aspect_ratio'])
    aspect_score = max(0, 1 - aspect_diff)
    
    # 综合评分
    total_score = (shape_score * 0.6 + area_ratio * 0.3 + aspect_score * 0.1)
    
    return total_score
```

### 4. 多线程并行检测
```python
def detect_gun_fast(self, image: np.ndarray, threshold: float = 0.5) -> List[Dict]:
    # 多线程并行检测
    futures = []
    for template_name in template_names:
        future = self.executor.submit(
            self.detect_weapon_worker, 
            template_name, image, threshold
        )
        futures.append(future)
    
    # 收集结果
    all_results = []
    for future in as_completed(futures):
        results = future.result()
        all_results.extend(results)
```

## 性能提升

### 速度提升
- **多线程处理**：利用多核CPU并行处理，速度提升 2-4 倍
- **减少计算量**：二值化处理减少了颜色计算
- **优化算法**：轮廓匹配比模板匹配更快

### 准确性提升
- **背景无关**：二值化处理消除了背景干扰
- **形状识别**：只关注形状特征，不受颜色变化影响
- **鲁棒性增强**：在不同光照和背景下都能稳定识别

## 使用方法

1. 运行 `启动快速识别.bat`
2. 按 `Tab` 键触发识别
3. 系统会自动进行快速识别并显示结果

## 注意事项

1. **首次运行**：需要加载模板，可能需要几秒钟
2. **CPU使用**：多线程会占用更多CPU资源
3. **内存使用**：预处理后的模板会占用一些内存
4. **阈值调整**：如果识别不准确，可以调整 `threshold` 参数

## 文件说明

- `game_detection_fast.py`：快速版本的主程序
- `启动快速识别.bat`：快速版本的启动脚本
- `res/`：模板资源文件夹
- `region_config.ini`：区域配置文件 
