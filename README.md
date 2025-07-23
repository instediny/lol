# lol
```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>英雄联盟英雄猜测游戏</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #0e0e1a;
            color: #f0f0f0;
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        .game-container {
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        .hero-card {
            background-color: #1a1a2e;
            border-radius: 8px;
            padding: 15px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
        }
        .filter-section {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            margin-bottom: 20px;
        }
        .filter-group {
            flex: 1;
            min-width: 200px;
        }
        button {
            background-color: #4a4a8a;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #6a6aaa;
        }
        #heroImage {
            max-width: 200px;
            border-radius: 4px;
            display: none;
        }
        .history-item {
            margin-bottom: 5px;
            padding: 5px;
            border-bottom: 1px solid #333;
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>英雄联盟英雄猜测游戏</h1>
        
        <div class="hero-card">
            <h2>当前猜测</h2>
            <div class="filter-section">
                <div class="filter-group">
                    <h3>性别</h3>
                    <select id="genderFilter">
                        <option value="any">任意</option>
                        <option value="male">男性</option>
                        <option value="female">女性</option>
                        <option value="other">其他</option>
                    </select>
                </div>
                
                <div class="filter-group">
                    <h3>上线年份</h3>
                    <select id="yearFilter">
                        <option value="any">任意</option>
                        <!-- 年份选项将通过JS动态生成 -->
                    </select>
                </div>
                
                <div class="filter-group">
                    <h3>位置</h3>
                    <select id="roleFilter">
                        <option value="any">任意</option>
                        <option value="top">上单</option>
                        <option value="jungle">打野</option>
                        <option value="mid">中单</option>
                        <option value="adc">ADC</option>
                        <option value="support">辅助</option>
                    </select>
                </div>
                
                <div class="filter-group">
                    <h3>出身国度</h3>
                    <select id="regionFilter">
                        <option value="any">任意</option>
                        <!-- 地区选项将通过JS动态生成 -->
                    </select>
                </div>
            </div>
            
            <button id="guessButton">猜测</button>
            <button id="randomGuessButton">随机猜测</button>
            <button id="restartButton">重新开始</button>
            
            <div id="feedback"></div>
            <img id="heroImage" alt="英雄图片">
            <div id="heroInfo"></div>
        </div>
        
        <div class="hero-card">
            <h2>猜测历史</h2>
            <div id="guessHistory"></div>
            <p>剩余可能英雄: <span id="remainingCount">0</span></p>
        </div>
    </div>

    <script>
        // 这里将包含游戏逻辑和英雄数据库
        // 实际实现时需要连接到后端API或包含完整英雄数据
    </script>
</body>
</html>
```

### 后端部分（Node.js示例）
```javascript
const express = require('express');
const cors = require('cors');
const app = express();
app.use(cors());
app.use(express.json());

// 模拟英雄数据库
const heroes = [
    { id: 1, name: "亚索", gender: "male", releaseYear: 2013, roles: ["mid", "top"], region: "艾欧尼亚", image: "yasuo.jpg" },
    { id: 2, name: "拉克丝", gender: "female", releaseYear: 2010, roles: ["mid", "support"], region: "德玛西亚", image: "lux.jpg" },
    // 更多英雄数据...
];

let currentHero = null;

// 获取随机英雄
app.get('/api/random-hero', (req, res) => {
    currentHero = heroes[Math.floor(Math.random() * heroes.length)];
    res.json({ 
        hint: `已选择一位英雄，你可以开始猜测了！总英雄数: ${heroes.length}` 
    });
});

// 处理猜测
app.post('/api/guess', (req, res) => {
    const { name, gender, year, role, region } = req.body;
    
    if (!currentHero) {
        return res.status(400).json({ error: "请先开始游戏" });
    }
    
    // 检查猜测是否正确
    const isCorrect = currentHero.name === name;
    
    // 过滤符合条件的英雄
    const filteredHeroes = heroes.filter(hero => {
        return (!gender || gender === "any" || hero.gender === gender) &&
               (!year || year === "any" || hero.releaseYear == year) &&
               (!role || role === "any" || hero.roles.includes(role)) &&
               (!region || region === "any" || hero.region === region);
    });
    
    res.json({
        isCorrect,
        currentHero: isCorrect ? currentHero : null,
        remainingCount: filteredHeroes.length,
        filteredHeroes: isCorrect ? [] : filteredHeroes.slice(0, 5) // 返回部分可能英雄作为提示
    });
});

// 获取英雄列表
app.get('/api/heroes', (req, res) => {
    res.json(heroes);
});

const PORT = 3000;
app.listen(PORT, () => {
    console.log(`服务器运行在 http://localhost:${PORT}`);
});
```
