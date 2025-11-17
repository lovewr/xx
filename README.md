
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>戒烟打卡 | 向健康出发</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', 'Microsoft YaHei', sans-serif;
        }
        body {
            background: linear-gradient(135deg, #e8f4f8 0%, #f0f8fb 100%);
            min-height: 100vh;
            padding: 20px;
            color: #2d3748;
        }
        .container {
            max-width: 600px;
            margin: 0 auto;
            background: white;
            border-radius: 16px;
            padding: 30px;
            box-shadow: 0 8px 24px rgba(0,0,0,0.08);
        }
        .header {
            text-align: center;
            margin-bottom: 30px;
        }
        .header h1 {
            color: #2b6cb0;
            font-size: 28px;
            margin-bottom: 8px;
        }
        .header p {
            color: #718096;
            font-size: 16px;
        }
        .stats {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 20px;
            margin-bottom: 30px;
        }
        .stat-card {
            background: #f7fafc;
            border-radius: 12px;
            padding: 20px;
            text-align: center;
            border: 1px solid #e2e8f0;
        }
        .stat-card h3 {
            color: #718096;
            font-size: 14px;
            margin-bottom: 10px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        .stat-card .value {
            font-size: 32px;
            font-weight: bold;
            color: #2b6cb0;
        }
        .stat-card .unit {
            color: #4a5568;
            font-size: 14px;
        }
        .check-in {
            text-align: center;
            margin-bottom: 30px;
        }
        #checkInBtn {
            background: #3182ce;
            color: white;
            border: none;
            padding: 16px 40px;
            border-radius: 8px;
            font-size: 18px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 12px rgba(49, 130, 206, 0.2);
        }
        #checkInBtn:hover {
            background: #2b6cb0;
            transform: translateY(-2px);
            box-shadow: 0 6px 16px rgba(49, 130, 206, 0.3);
        }
        #checkInBtn:disabled {
            background: #a0aec0;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }
        .quote {
            background: #fef7fb;
            border-left: 4px solid #9f7aea;
            padding: 20px;
            border-radius: 0 8px 8px 0;
            margin-bottom: 30px;
        }
        .quote p {
            color: #4a5568;
            font-size: 16px;
            line-height: 1.6;
            font-style: italic;
        }
        .quote .author {
            color: #718096;
            font-size: 14px;
            text-align: right;
            margin-top: 8px;
        }
        .history {
            margin-top: 30px;
        }
        .history h3 {
            color: #2d3748;
            font-size: 18px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
        }
        .history h3::before {
            content: '';
            display: inline-block;
            width: 4px;
            height: 20px;
            background: #3182ce;
            margin-right: 10px;
            border-radius: 2px;
        }
        .history-list {
            max-height: 200px;
            overflow-y: auto;
            padding-right: 10px;
        }
        .history-item {
            background: #f8f8f8;
            border-radius: 8px;
            padding: 12px 16px;
            margin-bottom: 8px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .history-date {
            color: #2d3748;
            font-weight: 500;
        }
        .history-check {
            color: #48bb78;
            font-size: 14px;
            display: flex;
            align-items: center;
        }
        .history-check::before {
            content: '✓';
            margin-right: 5px;
        }
        .footer {
            text-align: center;
            color: #718096;
            font-size: 14px;
            margin-top: 20px;
        }
        @media (max-width: 480px) {
            .stats {
                grid-template-columns: 1fr;
            }
            #checkInBtn {
                width: 100%;
                padding: 14px 0;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>戒烟打卡挑战</h1>
            <p id="startDate">开始于：未记录</p>
        </div>

        <div class="stats">
            <div class="stat-card">
                <h3>已坚持</h3>
                <div class="value" id="daysCount">0</div>
                <div class="unit">天</div>
            </div>
            <div class="stat-card">
                <h3>打卡次数</h3>
                <div class="value" id="checkInCount">0</div>
                <div class="unit">次</div>
            </div>
        </div>

        <div class="check-in">
            <button id="checkInBtn">今日打卡</button>
        </div>

        <div class="quote">
            <p id="quoteText">每一次拒绝香烟，都是对健康的投资。</p>
            <div class="author" id="quoteAuthor">—— 戒烟者联盟</div>
        </div>

        <div class="history">
            <h3>打卡记录</h3>
            <div class="history-list" id="historyList">
                <div class="history-item">
                    <span>暂无打卡记录</span>
                </div>
            </div>
        </div>

        <div class="footer">
            数据存储在本地，刷新页面不丢失 | 坚持就是胜利！
        </div>
    </div>

    <script>
        // 初始化数据
        const initData = {
            startDate: '',
            checkInDates: [],
            lastCheckIn: ''
        };

        // 获取本地存储数据
        function getData() {
            const data = localStorage.getItem('quitSmokingData');
            return data ? JSON.parse(data) : { ...initData };
        }

        // 保存数据到本地存储
        function saveData(data) {
            localStorage.setItem('quitSmokingData', JSON.stringify(data));
        }

        // 格式化日期
        function formatDate(date) {
            const year = date.getFullYear();
            const month = String(date.getMonth() + 1).padStart(2, '0');
            const day = String(date.getDate()).padStart(2, '0');
            return `${year}-${month}-${day}`;
        }

        // 计算已坚持天数
        function calculateDays(startDate) {
            if (!startDate) return 0;
            const start = new Date(startDate);
            const today = new Date();
            today.setHours(0, 0, 0, 0);
            const diffTime = today - start;
            return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
        }

        // 随机鼓励语录
        const quotes = [
            { text: "戒烟不是牺牲，而是给予自己健康和自由。", author: "—— 健康生活家" },
            { text: "每多坚持一天，离更健康的自己就更近一步。", author: "—— 自律者" },
            { text: "拒绝第一支烟，就战胜了最大的敌人。", author: "—— 戒烟成功者" },
            { text: "现在的忍耐，是为了未来更长久的快乐。", author: "—— 生活智者" },
            { text: "你不是在放弃什么，而是在赢得一切。", author: "—— 自我提升者" }
        ];

        // 更新语录
        function updateQuote() {
            const randomIndex = Math.floor(Math.random() * quotes.length);
            document.getElementById('quoteText').textContent = quotes[randomIndex].text;
            document.getElementById('quoteAuthor').textContent = quotes[randomIndex].author;
        }

        // 更新页面数据
        function updatePage() {
            const data = getData();
            const today = formatDate(new Date());

            // 更新开始日期
            if (data.startDate) {
                document.getElementById('startDate').textContent = `开始于：${data.startDate}`;
            }

            // 更新坚持天数
            const daysCount = calculateDays(data.startDate);
            document.getElementById('daysCount').textContent = daysCount;

            // 更新打卡次数
            document.getElementById('checkInCount').textContent = data.checkInDates.length;

            // 更新打卡按钮状态
            const checkInBtn = document.getElementById('checkInBtn');
            if (data.lastCheckIn === today) {
                checkInBtn.textContent = "今日已打卡";
                checkInBtn.disabled = true;
            } else {
                checkInBtn.textContent = "今日打卡";
                checkInBtn.disabled = false;
            }

            // 更新打卡记录
            const historyList = document.getElementById('historyList');
            if (data.checkInDates.length === 0) {
                historyList.innerHTML = '<div class="history-item"><span>暂无打卡记录</span></div>';
            } else {
                // 按日期倒序排列
                const sortedDates = [...data.checkInDates].sort((a, b) => new Date(b) - new Date(a));
                historyList.innerHTML = sortedDates.map(date => `
                    <div class="history-item">
                        <span class="history-date">${date}</span>
                        <span class="history-check">打卡成功</span>
                    </div>
                `).join('');
            }

            // 更新语录
            updateQuote();
        }

        // 打卡功能
        function checkIn() {
            const data = getData();
            const today = formatDate(new Date());

            // 设置开始日期（首次打卡时）
            if (!data.startDate) {
                data.startDate = today;
            }

            // 添加打卡日期
            data.checkInDates.push(today);
            data.lastCheckIn = today;

            // 保存数据
            saveData(data);

            // 更新页面
            updatePage();

            // 提示
            alert('打卡成功！坚持就是胜利～');
        }

        // 绑定事件
        document.getElementById('checkInBtn').addEventListener('click', checkIn);

        // 页面加载时更新
        window.addEventListener('load', updatePage);
    </script>
</body>
</html>
