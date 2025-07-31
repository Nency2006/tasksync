<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TaskSync Pro - Collaborative To-Do List</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#6366f1',
                        secondary: '#8b5cf6',
                        accent: '#06b6d4',
                        success: '#10b981',
                        warning: '#f59e0b',
                        danger: '#ef4444'
                    }
                }
            }
        }
    </script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .glass-effect {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        .task-card {
            transition: all 0.3s ease;
        }
        .task-card:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
        }
        .priority-high { border-left: 4px solid #ef4444; }
        .priority-medium { border-left: 4px solid #f59e0b; }
        .priority-low { border-left: 4px solid #10b981; }
        .fade-in {
            animation: fadeIn 0.5s ease-in;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="bg-gradient-to-br from-indigo-50 via-white to-purple-50 min-h-screen">
    <!-- Header -->
    <header class="bg-white shadow-sm border-b border-gray-100 sticky top-0 z-50">
        <div class="max-w-md mx-auto px-4 py-4">
            <div class="flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 bg-gradient-to-r from-primary to-secondary rounded-xl flex items-center justify-center">
                        <i class="fas fa-tasks text-white text-lg"></i>
                    </div>
                    <div>
                        <h1 class="text-xl font-bold text-gray-900">TaskSync Pro</h1>
                        <p class="text-xs text-gray-500">Collaborative Workspace</p>
                    </div>
                </div>
                <div class="flex items-center space-x-2">
                    <button id="userToggle" class="px-3 py-1.5 bg-primary text-white text-sm rounded-lg font-medium">
                        Assistant
                    </button>
                    <button id="syncBtn" class="p-2 text-gray-400 hover:text-primary transition-colors">
                        <i class="fas fa-sync-alt"></i>
                    </button>
                </div>
            </div>
        </div>
    </header>

    <div class="max-w-md mx-auto px-4 py-6">
        <!-- User Role Indicator -->
        <div id="roleIndicator" class="mb-6 p-4 bg-gradient-to-r from-primary to-secondary rounded-2xl text-white">
            <div class="flex items-center justify-between">
                <div>
                    <h2 class="font-semibold">Assistant Mode</h2>
                    <p class="text-sm opacity-90">You can add and manage tasks</p>
                </div>
                <i class="fas fa-user-plus text-2xl opacity-80"></i>
            </div>
        </div>

        <!-- Add Task Form (Assistant Only) -->
        <div id="addTaskSection" class="mb-6">
            <div class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6">
                <h3 class="text-lg font-semibold text-gray-900 mb-4 flex items-center">
                    <i class="fas fa-plus-circle text-primary mr-2"></i>
                    Add New Task
                </h3>
                
                <form id="taskForm" class="space-y-4">
                    <div>
                        <input type="text" id="taskTitle" placeholder="Task title..." 
                               class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-primary focus:border-transparent outline-none transition-all">
                    </div>
                    
                    <div class="grid grid-cols-2 gap-3">
                        <select id="taskCategory" class="px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-primary focus:border-transparent outline-none">
                            <option value="work">🏢 Work</option>
                            <option value="personal">👤 Personal</option>
                            <option value="shopping">🛒 Shopping</option>
                            <option value="health">💊 Health</option>
                            <option value="finance">💰 Finance</option>
                        </select>
                        
                        <select id="taskPriority" class="px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-primary focus:border-transparent outline-none">
                            <option value="low">🟢 Low</option>
                            <option value="medium">🟡 Medium</option>
                            <option value="high">🔴 High</option>
                        </select>
                    </div>
                    
                    <div>
                        <input type="date" id="taskDueDate" 
                               class="w-full px-4 py-3 border border-gray-200 rounded-xl focus:ring-2 focus:ring-primary focus:border-transparent outline-none">
                    </div>
                    
                    <button type="submit" class="w-full bg-gradient-to-r from-primary to-secondary text-white py-3 rounded-xl font-medium hover:shadow-lg transition-all">
                        <i class="fas fa-plus mr-2"></i>Add Task
                    </button>
                </form>
            </div>
        </div>

        <!-- Task Statistics -->
        <div class="grid grid-cols-3 gap-3 mb-6">
            <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
                <div class="text-2xl font-bold text-primary" id="totalTasks">0</div>
                <div class="text-xs text-gray-500">Total</div>
            </div>
            <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
                <div class="text-2xl font-bold text-success" id="completedTasks">0</div>
                <div class="text-xs text-gray-500">Done</div>
            </div>
            <div class="bg-white rounded-xl p-4 text-center shadow-sm border border-gray-100">
                <div class="text-2xl font-bold text-warning" id="pendingTasks">0</div>
                <div class="text-xs text-gray-500">Pending</div>
            </div>
        </div>

        <!-- Filter Tabs -->
        <div class="flex space-x-2 mb-6 bg-gray-100 p-1 rounded-xl">
            <button class="filter-tab active flex-1 py-2 px-4 rounded-lg text-sm font-medium transition-all" data-filter="all">All</button>
            <button class="filter-tab flex-1 py-2 px-4 rounded-lg text-sm font-medium transition-all" data-filter="pending">Pending</button>
            <button class="filter-tab flex-1 py-2 px-4 rounded-lg text-sm font-medium transition-all" data-filter="completed">Done</button>
        </div>

        <!-- Tasks List -->
        <div id="tasksList" class="space-y-3">
            <!-- Sample tasks will be populated here -->
        </div>

        <!-- Empty State -->
        <div id="emptyState" class="text-center py-12 hidden">
            <div class="w-20 h-20 bg-gray-100 rounded-full flex items-center justify-center mx-auto mb-4">
                <i class="fas fa-clipboard-list text-3xl text-gray-400"></i>
            </div>
            <h3 class="text-lg font-semibold text-gray-900 mb-2">No tasks yet</h3>
            <p class="text-gray-500 text-sm">Add your first task to get started!</p>
        </div>
    </div>

    <script>
        // App State
        let currentUser = 'assistant';
        let tasks = [];
        let currentFilter = 'all';

        // Sample data for demonstration
        const sampleTasks = [
            {
                id: 1,
                title: "Review quarterly financial reports",
                category: "work",
                priority: "high",
                dueDate: "2024-01-15",
                completed: false,
                createdAt: new Date().toISOString()
            },
            {
                id: 2,
                title: "Schedule team meeting for project kickoff",
                category: "work",
                priority: "medium",
                dueDate: "2024-01-12",
                completed: false,
                createdAt: new Date().toISOString()
            },
            {
                id: 3,
                title: "Buy groceries for weekend meal prep",
                category: "shopping",
                priority: "low",
                dueDate: "2024-01-10",
                completed: true,
                createdAt: new Date().toISOString()
            }
        ];

        // Initialize app
        function initApp() {
            // Load tasks from localStorage or use sample data
            const savedTasks = localStorage.getItem('taskSyncTasks');
            tasks = savedTasks ? JSON.parse(savedTasks) : sampleTasks;
            
            renderTasks();
            updateStats();
            setupEventListeners();
        }

        // Setup event listeners
        function setupEventListeners() {
            // User toggle
            document.getElementById('userToggle').addEventListener('click', toggleUser);
            
            // Task form
            document.getElementById('taskForm').addEventListener('submit', addTask);
            
            // Filter tabs
            document.querySelectorAll('.filter-tab').forEach(tab => {
                tab.addEventListener('click', (e) => setFilter(e.target.dataset.filter));
            });
            
            // Sync button
            document.getElementById('syncBtn').addEventListener('click', syncTasks);
        }

        // Toggle between Assistant and Sir
        function toggleUser() {
            currentUser = currentUser === 'assistant' ? 'sir' : 'assistant';
            updateUserInterface();
        }

        // Update UI based on current user
        function updateUserInterface() {
            const userToggle = document.getElementById('userToggle');
            const roleIndicator = document.getElementById('roleIndicator');
            const addTaskSection = document.getElementById('addTaskSection');
            
            if (currentUser === 'assistant') {
                userToggle.textContent = 'Assistant';
                roleIndicator.innerHTML = `
                    <div class="flex items-center justify-between">
                        <div>
                            <h2 class="font-semibold">Assistant Mode</h2>
                            <p class="text-sm opacity-90">You can add and manage tasks</p>
                        </div>
                        <i class="fas fa-user-plus text-2xl opacity-80"></i>
                    </div>
                `;
                addTaskSection.style.display = 'block';
            } else {
                userToggle.textContent = 'Sir';
                roleIndicator.innerHTML = `
                    <div class="flex items-center justify-between">
                        <div>
                            <h2 class="font-semibold">Sir's View</h2>
                            <p class="text-sm opacity-90">View and track task progress</p>
                        </div>
                        <i class="fas fa-eye text-2xl opacity-80"></i>
                    </div>
                `;
                addTaskSection.style.display = 'none';
            }
        }

        // Add new task
        function addTask(e) {
            e.preventDefault();
            
            const title = document.getElementById('taskTitle').value.trim();
            const category = document.getElementById('taskCategory').value;
            const priority = document.getElementById('taskPriority').value;
            const dueDate = document.getElementById('taskDueDate').value;
            
            if (!title) return;
            
            const newTask = {
                id: Date.now(),
                title,
                category,
                priority,
                dueDate,
                completed: false,
                createdAt: new Date().toISOString()
            };
            
            tasks.unshift(newTask);
            saveTasks();
            renderTasks();
            updateStats();
            
            // Reset form
            document.getElementById('taskForm').reset();
            
            // Show success feedback
            showNotification('Task added successfully!', 'success');
        }

        // Toggle task completion
        function toggleTask(taskId) {
            const task = tasks.find(t => t.id === taskId);
            if (task) {
                task.completed = !task.completed;
                saveTasks();
                renderTasks();
                updateStats();
                
                const status = task.completed ? 'completed' : 'reopened';
                showNotification(`Task ${status}!`, task.completed ? 'success' : 'info');
            }
        }

        // Delete task
        function deleteTask(taskId) {
            if (currentUser !== 'assistant') return;
            
            tasks = tasks.filter(t => t.id !== taskId);
            saveTasks();
            renderTasks();
            updateStats();
            showNotification('Task deleted!', 'info');
        }

        // Set filter
        function setFilter(filter) {
            currentFilter = filter;
            
            // Update active tab
            document.querySelectorAll('.filter-tab').forEach(tab => {
                tab.classList.remove('active', 'bg-white', 'text-primary', 'shadow-sm');
                tab.classList.add('text-gray-600');
            });
            
            const activeTab = document.querySelector(`[data-filter="${filter}"]`);
            activeTab.classList.add('active', 'bg-white', 'text-primary', 'shadow-sm');
            activeTab.classList.remove('text-gray-600');
            
            renderTasks();
        }

        // Render tasks
        function renderTasks() {
            const tasksList = document.getElementById('tasksList');
            const emptyState = document.getElementById('emptyState');
            
            let filteredTasks = tasks;
            
            if (currentFilter === 'pending') {
                filteredTasks = tasks.filter(t => !t.completed);
            } else if (currentFilter === 'completed') {
                filteredTasks = tasks.filter(t => t.completed);
            }
            
            if (filteredTasks.length === 0) {
                tasksList.innerHTML = '';
                emptyState.classList.remove('hidden');
                return;
            }
            
            emptyState.classList.add('hidden');
            
            tasksList.innerHTML = filteredTasks.map(task => `
                <div class="task-card bg-white rounded-2xl p-4 shadow-sm border border-gray-100 priority-${task.priority} fade-in">
                    <div class="flex items-start space-x-3">
                        <button onclick="toggleTask(${task.id})" class="mt-1 w-5 h-5 rounded-full border-2 flex items-center justify-center transition-all ${
                            task.completed 
                                ? 'bg-success border-success text-white' 
                                : 'border-gray-300 hover:border-primary'
                        }">
                            ${task.completed ? '<i class="fas fa-check text-xs"></i>' : ''}
                        </button>
                        
                        <div class="flex-1 min-w-0">
                            <h4 class="font-medium text-gray-900 ${task.completed ? 'line-through opacity-60' : ''}">${task.title}</h4>
                            
                            <div class="flex items-center space-x-4 mt-2 text-sm text-gray-500">
                                <span class="flex items-center">
                                    ${getCategoryIcon(task.category)} ${task.category}
                                </span>
                                <span class="flex items-center">
                                    ${getPriorityIcon(task.priority)} ${task.priority}
                                </span>
                                ${task.dueDate ? `<span class="flex items-center"><i class="far fa-calendar mr-1"></i> ${formatDate(task.dueDate)}</span>` : ''}
                            </div>
                        </div>
                        
                        ${currentUser === 'assistant' ? `
                            <button onclick="deleteTask(${task.id})" class="p-2 text-gray-400 hover:text-danger transition-colors">
                                <i class="fas fa-trash-alt text-sm"></i>
                            </button>
                        ` : ''}
                    </div>
                </div>
            `).join('');
        }

        // Helper functions
        function getCategoryIcon(category) {
            const icons = {
                work: '🏢',
                personal: '👤',
                shopping: '🛒',
                health: '💊',
                finance: '💰'
            };
            return icons[category] || '📋';
        }

        function getPriorityIcon(priority) {
            const icons = {
                high: '🔴',
                medium: '🟡',
                low: '🟢'
            };
            return icons[priority] || '⚪';
        }

        function formatDate(dateString) {
            const date = new Date(dateString);
            return date.toLocaleDateString('en-US', { month: 'short', day: 'numeric' });
        }

        // Update statistics
        function updateStats() {
            const total = tasks.length;
            const completed = tasks.filter(t => t.completed).length;
            const pending = total - completed;
            
            document.getElementById('totalTasks').textContent = total;
            document.getElementById('completedTasks').textContent = completed;
            document.getElementById('pendingTasks').textContent = pending;
        }

        // Save tasks to localStorage
        function saveTasks() {
            localStorage.setItem('taskSyncTasks', JSON.stringify(tasks));
        }

        // Sync tasks (simulate real-time sync)
        function syncTasks() {
            const syncBtn = document.getElementById('syncBtn');
            syncBtn.innerHTML = '<i class="fas fa-spinner fa-spin"></i>';
            
            setTimeout(() => {
                syncBtn.innerHTML = '<i class="fas fa-sync-alt"></i>';
                showNotification('Tasks synced successfully!', 'success');
            }, 1000);
        }

        // Show notification
        function showNotification(message, type = 'info') {
            const notification = document.createElement('div');
            notification.className = `fixed top-20 right-4 px-4 py-3 rounded-lg text-white text-sm font-medium z-50 transition-all transform translate-x-full`;
            
            const colors = {
                success: 'bg-success',
                info: 'bg-primary',
                warning: 'bg-warning',
                error: 'bg-danger'
            };
            
            notification.classList.add(colors[type] || colors.info);
            notification.textContent = message;
            
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.classList.remove('translate-x-full');
            }, 100);
            
            setTimeout(() => {
                notification.classList.add('translate-x-full');
                setTimeout(() => notification.remove(), 300);
            }, 3000);
        }

        // Initialize app when DOM is loaded
        document.addEventListener('DOMContentLoaded', initApp);
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'967c46db64cc3b3a',t:'MTc1Mzk1NjMyMS4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
