<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>聊天记录分析工具</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.4/dist/chart.umd.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5.0.0/dist/tesseract.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/@vane/google-translate-api@1.0.5/dist/index.min.js"></script>
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
    body {
      background: linear-gradient(135deg, #1e293b, #2d3748);
      color: #e2e8f0;
      font-family: 'Inter', sans-serif;
      margin: 0;
      padding: 0;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .container {
      max-width: 1200px;
      width: 100%;
      padding: 2rem;
      background: rgba(55, 65, 81, 0.9);
      border-radius: 1rem;
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    }
    .header {
      text-align: center;
      margin-bottom: 2rem;
      color: #60a5fa;
      font-size: 2.5rem;
      font-weight: 700;
      text-shadow: 0 2px 4px rgba(96, 165, 250, 0.3);
    }
    .upload-section {
      background: #374151;
      padding: 2rem;
      border-radius: 0.75rem;
      box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
      margin-bottom: 2rem;
      text-align: center;
      transition: transform 0.3s;
    }
    .upload-section:hover {
      transform: translateY(-5px);
    }
    .results-section {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
      gap: 2rem;
    }
    .card {
      background: #4b5563;
      padding: 1.5rem;
      border-radius: 0.75rem;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
      transition: transform 0.3s, box-shadow 0.3s;
    }
    .card:hover {
      transform: translateY(-5px);
      box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
    }
    .chart-container {
      height: 300px;
      width: 100%;
    }
    .chat-records {
      max-height: 400px;
      overflow-y: auto;
      background: #1f2937;
      padding: 1rem;
      border-radius: 0.5rem;
      line-height: 1.6;
      scrollbar-width: thin;
      scrollbar-color: #60a5fa #1f2937;
    }
    .chat-records::-webkit-scrollbar {
      width: 8px;
    }
    .chat-records::-webkit-scrollbar-thumb {
      background: #60a5fa;
      border-radius: 4px;
    }
    .error {
      color: #f87171;
      font-weight: 600;
      margin-top: 1rem;
      text-align: center;
    }
    button {
      background: #3b82f6;
      color: white;
      padding: 0.75rem 1.5rem;
      border: none;
      border-radius: 0.5rem;
      cursor: pointer;
      font-weight: 600;
      transition: background 0.3s, transform 0.2s;
    }
    button:hover {
      background: #2563eb;
      transform: scale(1.05);
    }
    button:disabled {
      background: #6b7280;
      cursor: not-allowed;
    }
    input[type="file"] {
      margin-bottom: 1rem;
      color: #e2e8f0;
      width: 100%;
      padding: 0.75rem;
      background: #1f2937;
      border-radius: 0.5rem;
      border: 1px solid #4b5563;
    }
    h2 {
      font-size: 1.5rem;
      font-weight: 600;
      margin-bottom: 1rem;
      color: #93c5fd;
      border-bottom: 2px solid #60a5fa;
      padding-bottom: 0.5rem;
    }
    ul {
      list-style-type: disc;
      padding-left: 1.5rem;
    }
    li {
      margin-bottom: 0.75rem;
      cursor: pointer;
      transition: color 0.3s;
    }
    li:hover {
      color: #60a5fa;
    }
    .text-blue-600 {
      color: #2563eb;
    }
    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.6);
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
    .modal-content {
      background: #374151;
      padding: 2rem;
      border-radius: 0.75rem;
      max-height: 85vh;
      overflow-y: auto;
      width: 90%;
      max-width: 900px;
      box-shadow: 0 10px 20px rgba(0, 0, 0, 0.3);
    }
    .close {
      color: #f87171;
      float: right;
      font-size: 1.75rem;
      cursor: pointer;
      transition: color 0.3s;
    }
    .close:hover {
      color: #dc2626;
    }
    .loader {
      display: none;
      border: 4px solid #f3f4f6;
      border-top: 4px solid #60a5fa;
      border-radius: 50%;
      width: 40px;
      height: 40px;
      animation: spin 1s linear infinite;
      margin: 1rem auto;
    }
    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>聊天记录分析工具</h1>
    </div>
    <div class="upload-section">
      <input type="file" id="chatFile" accept=".txt,.json,.html,.jpg,.png" multiple>
      <button id="analyzeButton" onclick="analyzeChat()">分析</button>
      <div class="loader" id="loader"></div>
      <p id="errorMessage" class="error"></p>
    </div>
    <div class="results-section">
      <div class="card">
        <h2>日活跃用户</h2>
        <div class="chart-container">
          <canvas id="dailyActiveUsersChart"></canvas>
        </div>
      </div>
      <div class="card">
        <h2>活跃用户分布</h2>
        <div class="chart-container">
          <canvas id="activeUsersBarChart"></canvas>
        </div>
      </div>
      <div class="card">
        <h2>对话高峰期分布</h2>
        <div class="chart-container">
          <canvas id="peakHoursChart"></canvas>
        </div>
      </div>
      <div class="card">
        <h2>聊天主题分布</h2>
        <div class="chart-container">
          <canvas id="themesPieChart"></canvas>
        </div>
      </div>
      <div class="card">
        <h2>用户情绪分析</h2>
        <div class="chart-container">
          <canvas id="sentimentChart"></canvas>
        </div>
      </div>
      <div class="card">
        <h2>活跃用户排行</h2>
        <ul id="activeUsersList"></ul>
      </div>
      <div class="card">
        <h2>关键主题提到最多的</h2>
        <ul id="themesList"></ul>
      </div>
      <div class="card">
        <h2>所有聊天记录</h2>
        <div id="chatRecords" class="chat-records"></div>
      </div>
      <div class="card">
        <h2>分析总结</h2>
        <p id="summary"></p>
      </div>
    </div>
  </div>
  <div id="userModal" class="modal">
    <div class="modal-content">
      <span class="close" onclick="closeModal()">×</span>
      <h2 id="modalUserTitle"></h2>
      <div id="modalUserMessages" class="chat-records"></div>
    </div>
  </div>
  <div id="themeModal" class="modal">
    <div class="modal-content">
      <span class="close" onclick="closeThemeModal()">×</span>
      <h2 id="modalThemeTitle"></h2>
      <div id="modalThemeMessages" class="chat-records"></div>
    </div>
  </div>

  <script>
    let chatData = [];
    let imageData = [];
    let dailyActiveUsersChart, activeUsersBarChart, themesPieChart, peakHoursChart, sentimentChart;

    // 显示错误消息
    function showError(message) {
      const errorElement = document.getElementById('errorMessage');
      errorElement.textContent = message;
      setTimeout(() => {
        errorElement.textContent = '';
      }, 5000);
    }

    // 关闭用户模态框
    function closeModal() {
      document.getElementById('userModal').style.display = 'none';
    }

    // 关闭主题模态框
    function closeThemeModal() {
      document.getElementById('themeModal').style.display = 'none';
    }

    // 显示用户消息详情
    function showUserDetails(user) {
      const modal = document.getElementById('userModal');
      const modalTitle = document.getElementById('modalUserTitle');
      const modalMessages = document.getElementById('modalUserMessages');
      modalTitle.textContent = `用户 ${user} 的消息记录`;
      const userMessages = chatData.filter(chat => chat.user === user);
      modalMessages.innerHTML = userMessages
        .map(chat => `<p><span class="text-blue-600">${chat.time}:</span> ${chat.translatedMessage || chat.message}</p>`)
        .join('');
      modal.style.display = 'flex';
    }

    // 显示主题相关消息详情
    function showThemeDetails(theme) {
      const modal = document.getElementById('themeModal');
      const modalTitle = document.getElementById('modalThemeTitle');
      const modalMessages = document.getElementById('modalThemeMessages');
      modalTitle.textContent = `主题：${theme} 的消息记录`;
      const themeMessages = chatData.filter(chat => chat.mentionedThemes && chat.mentionedThemes.includes(theme));
      modalMessages.innerHTML = themeMessages
        .map(chat => `<p><span class="text-blue-600">${chat.time} ${chat.user}:</span> ${chat.translatedMessage || chat.message}</p>`)
        .join('');
      modal.style.display = 'flex';
    }

    // 翻译葡萄牙语为中文
    async function translateText(text) {
      try {
        const translate = window.GoogleTranslateApi;
        const result = await translate(text, { from: 'pt', to: 'zh-CN' });
        return result.text;
      } catch (error) {
        console.error('翻译失败:', error);
        return text;
      }
    }

    // 解析图片中的文案
    async function extractTextFromImage(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = async (e) => {
          const img = new Image();
          img.src = e.target.result;
          img.onload = async () => {
            const { data: { text } } = await Tesseract.recognize(img, 'por');
            const translatedText = await translateText(text.trim());
            resolve(translatedText || '');
          };
          img.onerror = () => reject(new Error('图片解析失败'));
        };
        reader.onerror = () => reject(new Error('图片读取失败'));
        reader.readAsDataURL(file);
      });
    }

    // 动态提取主题
    function extractThemes(messages) {
      const phraseCounts = {};
      const themeMap = {};

      // 提取 2-4 个词的短语作为潜在主题
      messages.forEach(chat => {
        const text = (chat.message + ' ' + (chat.translatedMessage || '')).toLowerCase();
        const words = text.split(/\s+/).filter(word => word.length > 2);
        for (let i = 0; i < words.length; i++) {
          for (let len = 2; len <= 4 && i + len <= words.length; len++) {
            const phrase = words.slice(i, i + len).join(' ');
            phraseCounts[phrase] = (phraseCounts[phrase] || 0) + 1;
          }
        }
      });

      // 筛选高频短语（至少出现 3 次）作为主题
      const themes = Object.entries(phraseCounts)
        .filter(([_, count]) => count >= 3)
        .sort((a, b) => b[1] - a[1])
        .slice(0, 15)
        .map(([phrase]) => phrase);

      // 将消息与主题关联
      messages.forEach(chat => {
        const text = (chat.message + ' ' + (chat.translatedMessage || '')).toLowerCase();
        chat.mentionedThemes = [];
        themes.forEach(theme => {
          if (text.includes(theme)) {
            chat.mentionedThemes.push(theme);
            themeMap[theme] = (themeMap[theme] || 0) + 1;
          }
        });
      });

      return { themes, themeMap };
    }

    // 解析聊天记录文件
    async function parseChatFile(file) {
      return new Promise((resolve) => {
        const reader = new FileReader();
        reader.onload = async (e) => {
          try {
            const text = e.target.result;
            let parsedData = [];

            // 解析 TXT 文件
            if (file.name.endsWith('.txt')) {
              const lines = text.split('\n').filter(line => line.trim());
              for (let line of lines) {
                const match = line.match(/^\[?(\d{4}-\d{2}-\d{2}\s+\d{2}:\d{2}(:\d{2})?)?\]?[\s\[]*([^:]+)?[:\]]?\s*(.+)/);
                if (match) {
                  const time = match[1] || new Date().toISOString().slice(0, 19).replace('T', ' ');
                  const user = match[3] ? match[3].trim() : '未知用户';
                  const message = match[4].trim();
                  const translatedMessage = await translateText(message);
                  parsedData.push({ time, user, message, translatedMessage });
                } else {
                  const translatedMessage = await translateText(line.trim());
                  parsedData.push({
                    time: new Date().toISOString().slice(0, 19).replace('T', ' '),
                    user: '未知用户',
                    message: line.trim(),
                    translatedMessage
                  });
                }
              }
            }

            // 解析 JSON 文件
            else if (file.name.endsWith('.json')) {
              try {
                const jsonData = JSON.parse(text);
                parsedData = Array.isArray(jsonData) ? jsonData : [jsonData];
                for (let item of parsedData) {
                  const time = item.time || new Date().toISOString().slice(0, 19).replace('T', ' ');
                  const user = item.user || '未知用户';
                  const message = item.message || '';
                  const translatedMessage = await translateText(message);
                  item.time = time;
                  item.user = user;
                  item.message = message;
                  item.translatedMessage = translatedMessage;
                }
              } catch (e) {
                parsedData = [{ time: new Date().toISOString().slice(0, 19).replace('T', ' '), user: '未知用户', message: text, translatedMessage: await translateText(text) }];
              }
            }

            // 解析 HTML 文件
            else if (file.name.endsWith('.html')) {
              const parser = new DOMParser();
              const doc = parser.parseFromString(text, 'text/html');
              const elements = Array.from(doc.getElementsByTagName('*')).filter(el => el.textContent.trim());
              let currentDate = '';
              let currentTime = '';
              let currentUser = '';
              let messageBuffer = [];

              const monthMap = {
                'January': '01', 'February': '02', 'March': '03', 'April': '04', 'May': '05', 'June': '06',
                'July': '07', 'August': '08', 'September': '09', 'October': '10', 'November': '11', 'December': '12'
              };

              for (let el of elements) {
                const line = el.textContent.trim();

                // 匹配日期行，如 "26 January 2025"
                const dateMatch = line.match(/(\d{1,2})\s+(January|February|March|April|May|June|July|August|September|October|November|December)\s+(\d{4})/);
                if (dateMatch) {
                  const day = String(dateMatch[1]).padStart(2, '0');
                  const month = monthMap[dateMatch[2]];
                  const year = dateMatch[3];
                  currentDate = `${year}-${month}-${day}`;
                  continue;
                }

                // 匹配时间行，如 "18:02"
                const timeMatch = line.match(/^(\d{2}:\d{2})$/);
                if (timeMatch) {
                  currentTime = timeMatch[1] + ':00';
                  continue;
                }

                // 匹配用户信息行
                const userMatch1 = line.match(/^\[\s*(\d+)\s*\]\s*([\w\s]+)$/); // 如 "[ 304469805 ] Gerlane Barros"
                const userMatch2 = line.match(/^(6733 Agente)$/); // 如 "6733 Agente"
                if (userMatch1 || userMatch2) {
                  if (currentUser && messageBuffer.length > 0) {
                    const fullTime = `${currentDate} ${currentTime}`;
                    const message = messageBuffer.join(' ');
                    const translatedMessage = await translateText(message);
                    parsedData.push({ time: fullTime, user: currentUser, message, translatedMessage });
                    messageBuffer = [];
                  }
                  currentUser = userMatch1 ? userMatch1[2].trim() : userMatch2[1].trim();
                  continue;
                }

                // 跳过标记行
                if (line === '[B' || line === '6') continue;

                // 跳过不需要的行
                if (line.includes('In reply to this message') || line.includes('Sticker') || line.includes('Outgoing') || line.match(/^\s*❤\s*$/) || line.match(/^\s*👏\s*$/)) continue;

                // 剩余的行作为消息内容
                if (currentUser && currentTime && currentDate) {
                  messageBuffer.push(line);
                } else if (line) {
                  const translatedMessage = await translateText(line);
                  parsedData.push({
                    time: new Date().toISOString().slice(0, 19).replace('T', ' '),
                    user: '未知用户',
                    message: line,
                    translatedMessage
                  });
                }
              }

              if (currentUser && messageBuffer.length > 0) {
                const fullTime = `${currentDate} ${currentTime}`;
                const message = messageBuffer.join(' ');
                const translatedMessage = await translateText(message);
                parsedData.push({ time: fullTime, user: currentUser, message, translatedMessage });
              }
            }

            // 解析图片文件
            else if (file.type.startsWith('image/')) {
              const text = await extractTextFromImage(file);
              if (text) {
                parsedData.push({
                  time: new Date().toISOString().slice(0, 19).replace('T', ' '),
                  user: 'ImageExtract',
                  message: text,
                  translatedMessage: text
                });
              }
            }

            if (parsedData.length === 0) {
              parsedData.push({
                time: new Date().toISOString().slice(0, 19).replace('T', ' '),
                user: '未知用户',
                message: '无法解析有效内容',
                translatedMessage: '无法解析有效内容'
              });
            }
            resolve(parsedData);
          } catch (error) {
            resolve([{
              time: new Date().toISOString().slice(0, 19).replace('T', ' '),
              user: '未知用户',
              message: `解析错误: ${error.message}`,
              translatedMessage: `解析错误: ${error.message}`
            }]);
          }
        };
        reader.onerror = () => resolve([{
          time: new Date().toISOString().slice(0, 19).replace('T', ' '),
          user: '未知用户',
          message: '文件读取失败',
          translatedMessage: '文件读取失败'
        }]);
        reader.readAsText(file);
      });
    }

    // 分析聊天记录
    async function analyzeChat() {
      const fileInput = document.getElementById('chatFile');
      const analyzeButton = document.getElementById('analyzeButton');
      const loader = document.getElementById('loader');
      if (!fileInput.files.length) {
        showError('请先上传聊天记录文件或图片！');
        return;
      }

      analyzeButton.disabled = true;
      loader.style.display = 'block';
      chatData = [];
      imageData = [];
      try {
        for (let file of fileInput.files) {
          const data = await parseChatFile(file);
          if (file.type.startsWith('image/')) {
            imageData.push(...data);
          } else {
            chatData.push(...data);
          }
        }
        chatData.push(...imageData);

        // 日活跃用户
        const dailyActive = {};
        chatData.forEach(chat => {
          const date = chat.time.split(' ')[0];
          if (!dailyActive[date]) dailyActive[date] = new Set();
          dailyActive[date].add(chat.user);
        });
        const dailyActiveData = Object.keys(dailyActive).map(date => ({
          date,
          count: dailyActive[date].size
        })).sort((a, b) => new Date(a.date) - new Date(b.date));

        // 群聊最活跃时间
        const hourlyActive = Array(24).fill(0);
        chatData.forEach(chat => {
          const hour = parseInt(chat.time.split(' ')[1].split(':')[0]);
          hourlyActive[hour]++;
        });
        const peakHour = hourlyActive.indexOf(Math.max(...hourlyActive));
        const peakHourStr = `${String(peakHour).padStart(2, '0')}:00 - ${String(peakHour + 1).padStart(2, '0')}:00`;

        // 活跃用户分布
        const userActivity = {};
        chatData.forEach(chat => {
          userActivity[chat.user] = (userActivity[chat.user] || 0) + 1;
        });
        const sortedUsers = Object.entries(userActivity).sort((a, b) => b[1] - a[1]);

        // 动态提取主题
        const { themes, themeMap } = extractThemes(chatData);
        const sortedThemes = Object.entries(themeMap).sort((a, b) => b[1] - a[1]);
        const topTheme = sortedThemes[0] ? `${sortedThemes[0][0]} (${sortedThemes[0][1]} 次)` : '无';

        // 聊天主题分析（基于动态主题）
        const themeCounts = {};
        themes.slice(0, 5).forEach(theme => {
          themeCounts[theme] = themeMap[theme] || 0;
        });

        // 用户情绪分析
        const sentiments = {
          '积极': ['feliz', 'ótimo', 'bom', 'obrigado', 'gostei', '开心', '很好', '谢谢'],
          '消极': ['problema', 'ruim', 'triste', 'raiva', 'desesperada', '问题', '不好', '难过', '生气'],
          '中性': []
        };
        const sentimentCounts = { '积极': 0, '消极': 0, '中性': 0 };
        chatData.forEach(chat => {
          const text = (chat.message + ' ' + (chat.translatedMessage || '')).toLowerCase();
          let sentiment = '中性';
          if (Object.keys(sentiments['积极']).some(keyword => text.includes(keyword))) {
            sentiment = '积极';
          } else if (Object.keys(sentiments['消极']).some(keyword => text.includes(keyword))) {
            sentiment = '消极';
          }
          sentimentCounts[sentiment]++;
          chat.sentiment = sentiment;
        });

        // 更新图表
        if (dailyActiveUsersChart) dailyActiveUsersChart.destroy();
        const dailyCtx = document.getElementById('dailyActiveUsersChart').getContext('2d');
        dailyActiveUsersChart = new Chart(dailyCtx, {
          type: 'line',
          data: {
            labels: dailyActiveData.map(d => d.date),
            datasets: [{
              label: '日活跃用户',
              data: dailyActiveData.map(d => d.count),
              borderColor: '#60a5fa',
              backgroundColor: 'rgba(96, 165, 250, 0.3)',
              fill: true,
              tension: 0.4
            }, {
              label: '消息数量',
              data: dailyActiveData.map(d => chatData.filter(c => c.time.startsWith(d.date)).length),
              borderColor: '#34d399',
              backgroundColor: 'rgba(52, 211, 153, 0.3)',
              fill: true,
              tension: 0.4
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              y: { beginAtZero: true, grid: { color: 'rgba(226, 232, 240, 0.1)' } },
              x: { grid: { color: 'rgba(226, 232, 240, 0.1)' } }
            },
            plugins: {
              legend: { labels: { color: '#e2e8f0' } }
            }
          }
        });

        if (activeUsersBarChart) activeUsersBarChart.destroy();
        const barCtx = document.getElementById('activeUsersBarChart').getContext('2d');
        activeUsersBarChart = new Chart(barCtx, {
          type: 'bar',
          data: {
            labels: sortedUsers.map(([user]) => user),
            datasets: [{
              label: '消息数',
              data: sortedUsers.map(([, count]) => count),
              backgroundColor: '#60a5fa',
              borderColor: '#60a5fa',
              borderWidth: 1
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              y: { beginAtZero: true, grid: { color: 'rgba(226, 232, 240, 0.1)' } },
              x: { grid: { color: 'rgba(226, 232, 240, 0.1)' } }
            },
            plugins: {
              legend: { display: false },
              tooltip: { mode: 'index' }
            }
          }
        });

        if (peakHoursChart) peakHoursChart.destroy();
        const peakCtx = document.getElementById('peakHoursChart').getContext('2d');
        peakHoursChart = new Chart(peakCtx, {
          type: 'bar',
          data: {
            labels: Array.from({ length: 24 }, (_, i) => `${String(i).padStart(2, '0')}:00`),
            datasets: [{
              label: '消息数量',
              data: hourlyActive,
              backgroundColor: '#34d399',
              borderColor: '#34d399',
              borderWidth: 1
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              y: { beginAtZero: true, grid: { color: 'rgba(226, 232, 240, 0.1)' } },
              x: { grid: { color: 'rgba(226, 232, 240, 0.1)' } }
            },
            plugins: {
              legend: { display: false },
              tooltip: { mode: 'index' }
            }
          }
        });

        if (themesPieChart) themesPieChart.destroy();
        const pieCtx = document.getElementById('themesPieChart').getContext('2d');
        themesPieChart = new Chart(pieCtx, {
          type: 'pie',
          data: {
            labels: Object.keys(themeCounts),
            datasets: [{
              data: Object.values(themeCounts),
              backgroundColor: ['#60a5fa', '#34d399', '#f59e0b', '#f87171', '#a3a3a3'],
              borderWidth: 1
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
              legend: { position: 'bottom', labels: { color: '#e2e8f0' } }
            }
          }
        });

        if (sentimentChart) sentimentChart.destroy();
        const sentimentCtx = document.getElementById('sentimentChart').getContext('2d');
        sentimentChart = new Chart(sentimentCtx, {
          type: 'pie',
          data: {
            labels: Object.keys(sentimentCounts),
            datasets: [{
              data: Object.values(sentimentCounts),
              backgroundColor: ['#34d399', '#f87171', '#a3a3a3'],
              borderWidth: 1
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
              legend: { position: 'bottom', labels: { color: 'e2e8f0' } }
            }
          }
        });

        // 活跃用户排行
        document.getElementById('activeUsersList').innerHTML = sortedUsers
          .map(([user, count]) => `<li onclick="showUserDetails('${user}')">${user}: ${count} 条消息</li>`)
          .join('');

        // 关键主题提到最多的
        document.getElementById('themesList').innerHTML = sortedThemes
          .map(([theme, count]) => `<li onclick="showThemeDetails('${theme}')">${theme}: ${count} 次</li>`)
          .join('');

        // 显示所有聊天记录
        document.getElementById('chatRecords').innerHTML = chatData
          .map(chat => {
            const contentAnalysis = chat.mentionedThemes || [];
            return `<p><span class="text-blue-600">${chat.time} ${chat.user}:</span> ${chat.message} <br><em>(翻译: ${chat.translatedMessage})</em> <br><small>提到: ${contentAnalysis.join(', ') || '无'}</small> <br><small>情绪: ${chat.sentiment}</small></p>`;
          })
          .join('');

        // 分析总结
        const avgActiveUsers = (Object.values(dailyActive).reduce((sum, set) => sum + set.size, 0) / Object.keys(dailyActive).length).toFixed(2);
        const maxActiveUsers = Math.max(...Object.values(dailyActive).map(set => set.size));
        document.getElementById('summary').innerHTML = `
          总消息数: ${chatData.length}<br>
          平均日活跃用户: ${avgActiveUsers}<br>
          活跃天数: ${Object.keys(dailyActive).length}<br>
          最多活跃人数: ${maxActiveUsers}<br>
          群活跃时间: ${peakHourStr}<br>
          最多提到关键主题: ${topTheme}<br>
          最活跃用户: ${sortedUsers[0]?.[0] || '无'} (${sortedUsers[0]?.[1] || 0} 条消息)
        `;
      } catch (error) {
        showError(`分析失败：${error.message}`);
      } finally {
        analyzeButton.disabled = false;
        loader.style.display = 'none';
      }
    }
  </script>
</body>
</html>
