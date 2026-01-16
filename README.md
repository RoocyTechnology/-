<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>高级投票计票器</title>
    <style>
        :root {
            --primary-color: #4a6baf;
            --secondary-color: #6c757d;
            --success-color: #28a745;
            --danger-color: #dc3545;
            --warning-color: #ffc107;
            --info-color: #17a2b8;
            --light-color: #f8f9fa;
            --dark-color: #343a40;
        }
        
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif;
        }
        
        body {
            background-color: #f5f7fa;
            color: #333;
            line-height: 1.6;
            padding: 20px;
        }
        
        .container {
            max-width: 1000px;
            margin: 0 auto;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            overflow: hidden;
        }
        
        header {
            background: linear-gradient(135deg, var(--primary-color), #3a5683);
            color: white;
            padding: 25px;
            text-align: center;
        }
        
        h1 {
            font-size: 2.2rem;
            margin-bottom: 10px;
        }
        
        .subtitle {
            font-size: 1.1rem;
            opacity: 0.9;
        }
        
        .main-content {
            display: flex;
            flex-wrap: wrap;
            padding: 20px;
        }
        
        .section {
            flex: 1;
            min-width: 300px;
            padding: 20px;
        }
        
        .section-title {
            font-size: 1.5rem;
            margin-bottom: 20px;
            color: var(--primary-color);
            border-bottom: 2px solid var(--light-color);
            padding-bottom: 10px;
        }
        
        .option {
            background-color: var(--light-color);
            border-radius: 8px;
            padding: 15px;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            transition: all 0.3s ease;
            border: 1px solid #e9ecef;
        }
        
        .option:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        
        .option-info {
            display: flex;
            align-items: center;
            flex-grow: 1;
        }
        
        .option-color {
            width: 18px;
            height: 18px;
            border-radius: 50%;
            margin-right: 12px;
            flex-shrink: 0;
        }
        
        .option-name {
            flex-grow: 1;
        }
        
        .option-actions {
            display: flex;
            gap: 10px;
        }
        
        .vote-btn {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 500;
            transition: all 0.3s;
            flex-shrink: 0;
        }
        
        .vote-btn:hover {
            background-color: #3a5683;
            transform: scale(1.03);
        }
        
        .vote-btn:active {
            transform: scale(0.98);
        }
        
        .vote-btn.voted {
            background-color: var(--success-color);
        }
        
        .downvote-btn {
            background-color: var(--danger-color);
            color: white;
            border: none;
            padding: 8px 16px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 500;
            transition: all 0.3s;
            flex-shrink: 0;
        }
        
        .downvote-btn:hover {
            background-color: #c82333;
            transform: scale(1.03);
        }
        
        .downvote-btn:active {
            transform: scale(0.98);
        }
        
        .downvote-btn.disabled {
            background-color: #e9ecef;
            color: #6c757d;
            cursor: not-allowed;
        }
        
        .stats-container {
            margin-top: 30px;
        }
        
        .total-votes {
            text-align: center;
            font-size: 1.1rem;
            margin: 20px 0;
            padding: 12px;
            background-color: var(--light-color);
            border-radius: 8px;
            font-weight: 500;
        }
        
        .result-item {
            margin-bottom: 20px;
        }
        
        .result-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            align-items: center;
        }
        
        .result-name {
            font-weight: 500;
        }
        
        .result-count {
            color: var(--secondary-color);
        }
        
        .result-bar {
            height: 25px;
            background-color: #e9ecef;
            border-radius: 5px;
            overflow: hidden;
        }
        
        .result-fill {
            height: 100%;
            border-radius: 5px;
            transition: width 0.5s ease;
            display: flex;
            align-items: center;
            padding-left: 10px;
            color: white;
            font-weight: 500;
            font-size: 0.9rem;
        }
        
        .winner-badge {
            background-color: var(--warning-color);
            color: #856404;
            padding: 3px 8px;
            border-radius: 10px;
            font-size: 0.75rem;
            margin-left: 8px;
            font-weight: 600;
        }
        
        .rank-badge {
            display: inline-block;
            width: 24px;
            height: 24px;
            background-color: var(--primary-color);
            color: white;
            border-radius: 50%;
            text-align: center;
            line-height: 24px;
            margin-right: 8px;
            font-size: 0.8rem;
            font-weight: bold;
        }
        
        .tied-rank {
            background-color: var(--secondary-color) !important;
        }
        
        /* 为前三名添加特殊样式 */
        .result-item:nth-child(1) .rank-badge {
            background-color: #ffd700; /* 金牌 */
        }
        
        .result-item:nth-child(2) .rank-badge {
            background-color: #c0c0c0; /* 银牌 */
        }
        
        .result-item:nth-child(3) .rank-badge {
            background-color: #cd7f32; /* 铜牌 */
        }
        
        .controls {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 20px;
            padding: 20px;
            border-top: 1px solid #e9ecef;
            flex-wrap: wrap;
        }
        
        .control-btn {
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-weight: 500;
            transition: all 0.3s;
            min-width: 120px;
        }
        
        .control-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        }
        
        .reset-btn {
            background-color: var(--danger-color);
            color: white;
        }
        
        .add-option-btn {
            background-color: var(--success-color);
            color: white;
        }
        
        .batch-add-btn {
            background-color: var(--info-color);
            color: white;
        }
        
        .export-btn {
            background-color: var(--info-color);
            color: white;
        }
        
        .clear-all-btn {
            background-color: var(--dark-color);
            color: white;
        }
        
        /* 模态框样式 */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }
        
        .modal-content {
            background-color: white;
            border-radius: 10px;
            width: 90%;
            max-width: 500px;
            padding: 25px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            border-bottom: 1px solid #e9ecef;
            padding-bottom: 15px;
        }
        
        .modal-title {
            font-size: 1.4rem;
            color: var(--primary-color);
            font-weight: 600;
        }
        
        .close-btn {
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: var(--secondary-color);
        }
        
        .modal-body {
            margin-bottom: 20px;
        }
        
        .modal-footer {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
        }
        
        .modal-textarea {
            width: 100%;
            height: 200px;
            padding: 12px;
            border: 1px solid #ddd;
            border-radius: 5px;
            resize: vertical;
            font-size: 1rem;
            line-height: 1.5;
        }
        
        .modal-textarea:focus {
            outline: none;
            border-color: var(--primary-color);
            box-shadow: 0 0 0 2px rgba(74, 107, 175, 0.2);
        }
        
        .modal-hint {
            font-size: 0.85rem;
            color: var(--secondary-color);
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>高级投票计票器</h1>
            <p class="subtitle">实时统计 · 可视化结果 · 数据持久化</p>
        </header>
        
        <div class="main-content">
            <section class="section">
                <h2 class="section-title">投票选项</h2>
                <div id="options-container">
                    <!-- 选项将通过JS动态生成 -->
                </div>
                
                <div class="total-votes">
                    总票数: <span id="total-votes-count">0</span>
                </div>
            </section>
            
            <section class="section">
                <h2 class="section-title">投票结果</h2>
                <div class="stats-container" id="results-container">
                    <!-- 结果将通过JS动态生成 -->
                </div>
            </section>
        </div>
        
        <div class="controls">
            <button class="control-btn reset-btn" id="reset-btn">重置投票</button>
            <button class="control-btn add-option-btn" id="add-option-btn">添加选项</button>
            <button class="control-btn batch-add-btn" id="batch-add-btn">批量添加</button>
            <button class="control-btn export-btn" id="export-btn">导出结果</button>
            <button class="control-btn clear-all-btn" id="clear-all-btn">全部清除</button>
        </div>
    </div>

    <!-- 批量添加选项模态框 -->
    <div class="modal" id="batch-modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3 class="modal-title">批量添加选项</h3>
                <button class="close-btn" id="close-batch-modal">&times;</button>
            </div>
            <div class="modal-body">
                <textarea class="modal-textarea" id="batch-options-text" placeholder="请输入选项名称，每行一个选项"></textarea>
                <p class="modal-hint">提示：每行输入一个选项名称，空行将被忽略</p>
            </div>
            <div class="modal-footer">
                <button class="control-btn reset-btn" id="cancel-batch-btn">取消</button>
                <button class="control-btn add-option-btn" id="confirm-batch-btn">确认添加</button>
            </div>
        </div>
    </div>

    <script>
        // 投票数据模型
        class VoteModel {
            constructor() {
                this.options = this.loadFromStorage() || this.getDefaultOptions();
            }
            
            getDefaultOptions() {
                return [
                    //添加默认条目格式为  { id: 1, name: "选项一", votes: 0, color: "#dc3545" }
                ];
            }
            
            addVote(optionId) {
                const option = this.options.find(opt => opt.id === optionId);
                if (option) {
                    option.votes++;
                    this.saveToStorage();
                    return true;
                }
                return false;
            }
            
            removeVote(optionId) {
                const option = this.options.find(opt => opt.id === optionId);
                if (option && option.votes > 0) {
                    option.votes--;
                    this.saveToStorage();
                    return true;
                }
                return false;
            }
            
            addOption(name) {
                if (!name || name.trim() === '') return false;
                
                const colors = ['#dc3545', '#28a745', '#4a6baf', '#ffc107', '#17a2b8', '#6f42c1', '#fd7e14'];
                const newId = this.options.length > 0 ? Math.max(...this.options.map(opt => opt.id)) + 1 : 1;
                
                this.options.push({
                    id: newId,
                    name: name.trim(),
                    votes: 0,
                    color: colors[Math.floor(Math.random() * colors.length)]
                });
                
                this.saveToStorage();
                return true;
            }
            
            addOptionsBatch(names) {
                if (!names || names.length === 0) return 0;
                
                let addedCount = 0;
                const colors = ['#dc3545', '#28a745', '#4a6baf', '#ffc107', '#17a2b8', '#6f42c1', '#fd7e14'];
                
                names.forEach(name => {
                    if (name && name.trim() !== '') {
                        const newId = this.options.length > 0 ? Math.max(...this.options.map(opt => opt.id)) + 1 : 1;
                        
                        this.options.push({
                            id: newId,
                            name: name.trim(),
                            votes: 0,
                            color: colors[Math.floor(Math.random() * colors.length)]
                        });
                        
                        addedCount++;
                    }
                });
                
                if (addedCount > 0) {
                    this.saveToStorage();
                }
                
                return addedCount;
            }
            
            resetVotes() {
                this.options.forEach(option => {
                    option.votes = 0;
                });
                this.saveToStorage();
            }
            
            clearAll() {
                this.options = this.getDefaultOptions();
                this.saveToStorage();
            }
            
            saveToStorage() {
                localStorage.setItem('voteData', JSON.stringify(this.options));
                return true;
            }
            
            loadFromStorage() {
                const data = localStorage.getItem('voteData');
                return data ? JSON.parse(data) : null;
            }
            
            getTotalVotes() {
                return this.options.reduce((sum, option) => sum + option.votes, 0);
            }
            
            getOptions() {
                return this.options;
            }
        }

        // 投票视图控制器
        class VoteController {
            constructor(model) {
                this.model = model;
                this.optionsContainer = document.getElementById('options-container');
                this.resultsContainer = document.getElementById('results-container');
                this.totalVotesCount = document.getElementById('total-votes-count');
                
                // 批量添加相关元素
                this.batchModal = document.getElementById('batch-modal');
                this.batchOptionsText = document.getElementById('batch-options-text');
                this.closeBatchModalBtn = document.getElementById('close-batch-modal');
                this.cancelBatchBtn = document.getElementById('cancel-batch-btn');
                this.confirmBatchBtn = document.getElementById('confirm-batch-btn');
                
                this.initElements();
                this.renderAll();
            }
            
            initElements() {
                document.getElementById('reset-btn').addEventListener('click', () => this.handleReset());
                document.getElementById('add-option-btn').addEventListener('click', () => this.handleAddOption());
                document.getElementById('batch-add-btn').addEventListener('click', () => this.showBatchModal());
                document.getElementById('export-btn').addEventListener('click', () => this.handleExport());
                document.getElementById('clear-all-btn').addEventListener('click', () => this.handleClearAll());
                
                // 批量添加模态框事件
                this.closeBatchModalBtn.addEventListener('click', () => this.hideBatchModal());
                this.cancelBatchBtn.addEventListener('click', () => this.hideBatchModal());
                this.confirmBatchBtn.addEventListener('click', () => this.handleBatchAdd());
                
                // 点击模态框外部关闭
                this.batchModal.addEventListener('click', (e) => {
                    if (e.target === this.batchModal) {
                        this.hideBatchModal();
                    }
                });
            }
            
            renderAll() {
                this.renderOptions();
                this.renderResults();
                this.updateTotalVotes();
            }
            
            renderOptions() {
                this.optionsContainer.innerHTML = '';
                
                this.model.getOptions().forEach(option => {
                    const optionElement = document.createElement('div');
                    optionElement.className = 'option';
                    optionElement.innerHTML = `
                        <div class="option-info">
                            <div class="option-color" style="background-color: ${option.color};"></div>
                            <span class="option-name">${option.name}</span>
                        </div>
                        <div class="option-actions">
                            <button class="vote-btn" data-id="${option.id}">投票</button>
                            <button class="downvote-btn" data-id="${option.id}" ${option.votes <= 0 ? 'disabled' : ''}>
                                减票
                            </button>
                        </div>
                    `;
                    
                    this.optionsContainer.appendChild(optionElement);
                });
                
                // 添加投票按钮事件
                document.querySelectorAll('.vote-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const optionId = parseInt(e.target.getAttribute('data-id'));
                        this.handleVote(optionId, e.target);
                    });
                });
                
                // 添加减票按钮事件
                document.querySelectorAll('.downvote-btn').forEach(btn => {
                    btn.addEventListener('click', (e) => {
                        const optionId = parseInt(e.target.getAttribute('data-id'));
                        this.handleRemoveVote(optionId, e.target);
                    });
                });
            }
            
            renderResults() {
                this.resultsContainer.innerHTML = '';
                const totalVotes = this.model.getTotalVotes();
                const options = [...this.model.getOptions()];
                
                // 按票数从高到低排序
                options.sort((a, b) => b.votes - a.votes);
                const maxVotes = options.length > 0 ? options[0].votes : 0;
                
                // 计算排名（处理并列情况）
                let currentRank = 1;
                let previousVotes = null;
                let previousRank = 1;
                
                options.forEach((option, index) => {
                    // 计算排名
                    if (previousVotes !== null && option.votes === previousVotes) {
                        // 票数相同，排名相同
                        option.rank = previousRank;
                    } else {
                        // 票数不同，更新排名
                        option.rank = currentRank;
                        previousRank = currentRank;
                    }
                    
                    currentRank++;
                    previousVotes = option.votes;
                    
                    const percentage = totalVotes > 0 ? (option.votes / totalVotes) * 100 : 0;
                    
                    const resultItem = document.createElement('div');
                    resultItem.className = 'result-item';
                    
                    // 添加并列排名样式类
                    const rankBadgeClass = (index > 0 && option.votes === options[index-1].votes) 
                        ? 'rank-badge tied-rank' 
                        : 'rank-badge';
                    
                    resultItem.innerHTML = `
                        <div class="result-header">
                            <span class="result-name">
                                <span class="${rankBadgeClass}">${option.rank}</span>
                                ${option.name}
                                ${option.votes === maxVotes && maxVotes > 0 ? '<span class="winner-badge">领先</span>' : ''}
                            </span>
                            <span class="result-count">${option.votes} 票 (${percentage.toFixed(1)}%)</span>
                        </div>
                        <div class="result-bar">
                            <div class="result-fill" style="width: ${percentage}%; background-color: ${option.color};">
                                ${percentage > 15 ? `${percentage.toFixed(1)}%` : ''}
                            </div>
                        </div>
                    `;
                    
                    this.resultsContainer.appendChild(resultItem);
                });
            }
            
            updateTotalVotes() {
                this.totalVotesCount.textContent = this.model.getTotalVotes();
            }
            
            handleVote(optionId, button) {
                if (this.model.addVote(optionId)) {
                    this.renderResults();
                    this.updateTotalVotes();
                    
                    // 视觉反馈
                    button.textContent = '✓ 已投';
                    button.classList.add('voted');
                    
                    // 启用对应的减票按钮
                    const downvoteBtn = button.parentElement.querySelector('.downvote-btn');
                    if (downvoteBtn) {
                        downvoteBtn.disabled = false;
                        downvoteBtn.classList.remove('disabled');
                    }
                    
                    setTimeout(() => {
                        button.textContent = '投票';
                        button.classList.remove('voted');
                    }, 1000);
                }
            }
            
            handleRemoveVote(optionId, button) {
                if (this.model.removeVote(optionId)) {
                    this.renderResults();
                    this.updateTotalVotes();
                    
                    // 视觉反馈
                    button.textContent = '✓ 已减';
                    button.classList.add('voted');
                    
                    setTimeout(() => {
                        button.textContent = '减票';
                        button.classList.remove('voted');
                        
                        // 如果票数为0，禁用按钮
                        const option = this.model.getOptions().find(opt => opt.id === optionId);
                        if (option && option.votes <= 0) {
                            button.disabled = true;
                            button.classList.add('disabled');
                        }
                    }, 1000);
                }
            }
            
            handleReset() {
                if (confirm('确定要重置所有投票数据吗？所有选项的票数将归零。')) {
                    this.model.resetVotes();
                    this.renderAll();
                }
            }
            
            handleAddOption() {
                const name = prompt('请输入新选项名称:');
                if (name && name.trim() !== '') {
                    if (this.model.addOption(name)) {
                        this.renderAll();
                    }
                }
            }
            
            showBatchModal() {
                this.batchOptionsText.value = '';
                this.batchModal.style.display = 'flex';
                this.batchOptionsText.focus();
            }
            
            hideBatchModal() {
                this.batchModal.style.display = 'none';
            }
            
            handleBatchAdd() {
                const text = this.batchOptionsText.value.trim();
                if (!text) {
                    alert('请输入选项名称');
                    return;
                }
                
                const names = text.split('\n')
                    .map(name => name.trim())
                    .filter(name => name !== '');
                
                if (names.length === 0) {
                    alert('请输入有效的选项名称');
                    return;
                }
                
                const addedCount = this.model.addOptionsBatch(names);
                
                if (addedCount > 0) {
                    this.renderAll();
                    this.hideBatchModal();
                    alert(`成功添加 ${addedCount} 个选项`);
                } else {
                    alert('未能添加任何选项，请检查输入');
                }
            }
            
            handleExport() {
                const totalVotes = this.model.getTotalVotes();
                let exportText = '投票结果\n==========\n\n';
                
                // 按票数排序
                const options = [...this.model.getOptions()].sort((a, b) => b.votes - a.votes);
                
                options.forEach((option, index) => {
                    const percentage = totalVotes > 0 ? (option.votes / totalVotes) * 100 : 0;
                    exportText += `第${index + 1}名: ${option.name}: ${option.votes} 票 (${percentage.toFixed(1)}%)\n`;
                });
                
                exportText += `\n总票数: ${totalVotes}\n`;
                exportText += `导出时间: ${new Date().toLocaleString()}`;
                
                // 创建下载链接
                const blob = new Blob([exportText], { type: 'text/plain' });
                const url = URL.createObjectURL(blob);
                const a = document.createElement('a');
                a.href = url;
                a.download = `投票结果_${new Date().toISOString().split('T')[0]}.txt`;
                document.body.appendChild(a);
                a.click();
                document.body.removeChild(a);
                URL.revokeObjectURL(url);
            }
            
            handleClearAll() {
                if (confirm('确定要清除所有数据吗？这将删除所有自定义选项并恢复为默认设置。')) {
                    this.model.clearAll();
                    this.renderAll();
                }
            }
        }

        // 初始化应用
        document.addEventListener('DOMContentLoaded', () => {
            const voteModel = new VoteModel();
            new VoteController(voteModel);
        });
    </script>
</body>
</html>
