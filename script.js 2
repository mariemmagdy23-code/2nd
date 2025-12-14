  <!-- JavaScript Logic -->
    <script>
        // --- Firebase Globals Setup ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-study-app-id';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : {};
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        // --- Firebase Imports and Init (Assuming necessary imports are available in the runtime environment) ---
        // Since we cannot use explicit imports in plain HTML script blocks inside this environment,
        // we'll rely on the global availability or implicit loading if the environment supports it.
        // For demonstration, we assume getFirestore, initializeApp, getAuth, signInWithCustomToken, etc., are available.
        
        let app;
        let db;
        let auth;
        let userId = 'anonymous'; // Default ID
        let isAuthReady = false;

        // --- API Configuration ---
        const API_KEY = ""; 
        const API_URL = "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=" + API_KEY;
        const LLM_MODEL = "gemini-2.5-flash-preview-09-2025";
        const MAX_RETRIES = 3;

        // --- Data Source ---
        const studyData = [
            // تم تحديث المكافأة لتكون رابط تشعبي (HTML)
            { week: 1, day: "الخميس", date: "11/12", subject: "مبادئ الرعاية الاجتماعية", type: "المرة الأولى", tasks: [{text: "إتقان أهم 5 مفاهيم رئيسية في الفصل 1"}, {text: "تلخيص التطور التاريخي للرعاية (الفصل 2)"}], reward: '<a href="https://dal.ahwaktv.net/watch.php?vid=f6891378" target="_blank" class="reward-link-embedded">🎁 المكافأة: فيديو المكافأة المخصص! 🎁</a>' },
            { week: 1, day: "الجمعة", date: "12/12", subject: "علم الاجتماع العام", type: "المرة الأولى", tasks: [{text: "تحديد مراحل نشأة علم الاجتماع ورواده (الفصل 1)"}, {text: "فهم مكونات وأبعاد الثقافة والمجتمع (الفصل 2)"}], reward: "كتابة 3 جمل تعريفية لكل رائد." },
            { week: 1, day: "السبت", date: "13/12", subject: "الشريعة الإسلامية", type: "المرة الأولى", tasks: [{text: "فهم أسس التشريع الإسلامي وأهدافه"}, {text: "التركيز على الفقه المالي (الأحكام العامة للمعاملات)"}], reward: "عمل خريطة ذهنية لمقاصد الشريعة الخمسة." },
            { week: 1, day: "الأحد", date: "14/12", subject: "الإحصاء الاجتماعي والتوصيفي", type: "التحدي الأول", tasks: [{text: "تحدي الإحصاء الثابت: حل ورق الملخص الأول"}, {text: "أو حل 30 مسألة على مقاييس النزعة المركزية"}], reward: "راحة 30 دقيقة بعد الإنجاز!", isSpecial: true },
            { week: 1, day: "الإثنين", date: "15/12", subject: "علم النفس العام", type: "المرة الأولى", tasks: [{text: "التفريق بين مفهوم وأهداف علم النفس وفروعه"}, {text: "شرح علاقة علم النفس بالعلوم الأخرى (الفصل 2)"}], reward: "تصميم جدول يوضح الفروع التطبيقية والنظرية." },
            { week: 1, day: "الثلاثاء", date: "16/12", subject: "الاقتصاد الاجتماعي", type: "المرة الأولى", tasks: [{text: "تحديد المشكلة الاقتصادية وعناصر الإنتاج"}, {text: "فهم العلاقة بين علمي الاجتماع والاقتصاد"}], reward: "الإجابة عن سؤال: كيف تحل الموارد النادرة المشكلة الاقتصادية؟" },
            { week: 1, day: "الأربعاء", date: "17/12", subject: "حاسب آلي ونظم معلومات", type: "المرة الأولى", tasks: [{text: "شرح مكونات الحاسب (مادية وبرمجية)"}, {text: "التمييز بين البيانات والمعلومات ونظم المعلومات"}], reward: "رسم تخطيطي لـ 'دورة معالجة البيانات'." },

            { week: 2, day: "الخميس", date: "18/12", subject: "مبادئ الرعاية الاجتماعية", type: "المرة الثانية", tasks: [{text: "تلخيص الأصول الدينية والاجتماعية للرعاية"}, {text: "مقارنة نماذج الرعاية في العصر الحديث"}], reward: "اختبار سريع مكون من 15 سؤالاً." },
            { week: 2, day: "الجمعة", date: "19/12", subject: "علم الاجتماع العام", type: "المرة الثانية", tasks: [{text: "فهم نظرية البنائية الوظيفية (أهم النقاط)"}, {text: "دراسة نظرية الصراع ومقارنتها بالبنائية"}], reward: "عمل قائمة بالنقاط الإيجابية والسلبية لكل نظرية." },
            { week: 2, day: "السبت", date: "20/12", subject: "الشريعة الإسلامية", type: "المرة الثانية", tasks: [{text: "تلخيص الأحكام الفقهية المتعلقة بالأسرة (الزواج والطلاق)"}, {text: "تلخيص الأحكام الخاصة بالعلاقات المجتمعية"}], reward: "مراجعة أحكام الميراث الأساسية." },
            { week: 2, day: "الأحد", date: "21/12", subject: "الإحصاء الاجتماعي والتوصيفي", type: "التحدي الثاني", tasks: [{text: "تحدي الإحصاء الثابت: حل ورق الملخص الثاني"}, {text: "أو حل 25 مسألة على مقاييس التشتت"}], reward: "مشروبك المفضل + حلقة مسلسل!", isSpecial: true },
            { week: 2, day: "الإثنين", date: "22/12", subject: "علم النفس العام", type: "المرة الثانية", tasks: [{text: "التمييز بين أنواع الدوافع (الأولية والثانوية)"}, {text: "تطبيقات الدوافع في الحياة اليومية"}], reward: "تحديد دافعين شخصيين وكيفية إدارتهما." },
            { week: 2, day: "الثلاثاء", date: "23/12", subject: "الاقتصاد الاجتماعي", type: "المرة الثانية", tasks: [{text: "فهم قانون الطلب والعوامل المؤثرة فيه"}, {text: "حساب أنواع المرونة وتطبيقاتها"}], reward: "حل 5 تمارين حسابية على مرونة الطلب." },
            { week: 2, day: "الأربعاء", date: "24/12", subject: "حاسب آلي ونظم معلومات", type: "المرة الثانية", tasks: [{text: "إدراك أهمية تكنولوجيا المعلومات للمؤسسات"}, {text: "فهم مراحل تحليل وتصميم الاستبيان إلكترونياً"}], reward: "تلخيص أدوات جمع البيانات الإلكترونية." },

            { week: 3, day: "الخميس", date: "25/12", subject: "مبادئ الرعاية الاجتماعية", type: "المرة الثالثة", tasks: [{text: "مراجعة رعاية ذوي الاحتياجات الخاصة (الجزء الأصعب)"}, {text: "مراجعة رعاية المسنين"}], reward: "تلخيص 3 مبادئ للخدمة الاجتماعية." },
            { week: 3, day: "الجمعة", date: "26/12", subject: "علم الاجتماع العام", type: "المرة الثالثة", tasks: [{text: "التعمق في فروع علم الاجتماع (الأسرة، الريف، الحضر)"}, {text: "ربط الفروع التي درستيها بالنظريات السابقة"}], reward: "وضع سؤال 'مقالي صعب' على المنهج حتى الآن." },
            { week: 3, day: "السبت", date: "27/12", subject: "الشريعة الإسلامية", type: "المرة الثالثة", tasks: [{text: "مراجعة القضايا الفقهية المعاصرة (النقاط الخلافية)"}, {text: "تثبيت 20 حكماً فقهياً أساسياً"}], reward: "مراجعة سريعة لأسباب الاختلافات الفقهية." },
            { week: 3, day: "الأحد", date: "28/12", subject: "الإحصاء الاجتماعي والتوصيفي", type: "التحدي الثالث", tasks: [{text: "تحدي الإحصاء الثابت: حل ورق الملخص الثالث"}, {text: "أو حل 20 مسألة على الارتباط والانحدار"}], reward: "استراحة طويلة مستحقة!", isSpecial: true },
            { week: 3, day: "الإثنين", date: "29/12", subject: "علم النفس العام", type: "المرة الثالثة", tasks: [{text: "مراجعة نظرية التعلم والتعديل السلوكي"}, {text: "تلخيص نظريات الذكاء والفروق الفردية"}], reward: "تصميم لعبة ذاكرة بسيطة لتطبيق مبادئ التعلم." },
            { week: 3, day: "الثلاثاء", date: "30/12", subject: "الاقتصاد الاجتماعي", type: "المرة الثالثة", tasks: [{text: "فهم سلوك المستهلك ونظرية المنفعة الحدية"}, {text: "تلخيص الإنتاج والتكاليف وأنواعها"}], reward: "رسم بياني لـ 'منحنى الإنتاج الكلي'." },
            { week: 3, day: "الأربعاء", date: "31/12", subject: "حاسب آلي ونظم معلومات", type: "المرة الثالثة", tasks: [{text: "مراجعة أساسيات قواعد البيانات واستخداماتها"}, {text: "تلخيص تطبيقات نظم المعلومات في مجالكِ"}], reward: "مراجعة 10 مصطلحات إنجليزية أساسية في المنهج." },

            { week: 4, day: "الخميس", date: "01/01", subject: "مراجعة مكثفة: (1) النظريات", type: "تركيز: الرعاية، الاجتماع، الشريعة", tasks: [{text: "حل أسئلة شاملة على نظريات الرعاية والاجتماع"}, {text: "مراجعة فقه المعاملات في الشريعة"}], reward: "تثبيت أهم 50 مصطلحًا علميًا.", isSpecial: true },
            { week: 4, day: "الجمعة", date: "02/01", subject: "مراجعة مكثفة: (2) الحسابات", type: "تركيز: الإحصاء، علم النفس، الاقتصاد", tasks: [{text: "حل تحدي الإحصاء النهائي (امتحان كامل)"}, {text: "مراجعة قوانين الاقتصاد وسلوك المستهلك"}], reward: "استراحة طويلة: مشاهدة فيلم خفيف!", isSpecial: true },
            { week: 4, day: "السبت", date: "03/01", subject: "مراجعة مكثفة: (3) إنهاء الخطة", type: "تركيز: الحاسب الآلي والمفاهيم", tasks: [{text: "مراجعة مفاهيم نظم المعلومات والمكونات"}, {text: "الاستعداد النهائي للامتحانات (نوم مبكر ومراجعة سريعة للملاحظات)"}], reward: "أنتِ مستعدة للامتحانات! 🎉", isSpecial: true }
        ];

        const weeksInfo = [
            { id: 1, label: "الأسبوع الأول (11/12 - 17/12)" },
            { id: 2, label: "الأسبوع الثاني (18/12 - 24/12)" },
            { id: 3, label: "الأسبوع الثالث (25/12 - 31/12)" },
            { id: 4, label: "الأسبوع الرابع (01/01 - 03/01)" }
        ];

        // --- State Management ---
        let progressState = JSON.parse(localStorage.getItem('studyProgressV2')) || {};
        let currentSimulatedDate = localStorage.getItem('simulatedDate') ? new Date(localStorage.getItem('simulatedDate')) : new Date(2025, 11, 11); // Start date: Dec 11, 2025
        let currentView = 'weekly';
        let currentWeek = 1;
        
        const REAL_START_DATE = new Date(2025, 11, 11); // 11 ديسمبر 2025

        // --- Utility Functions ---

        /**
         * Calculates the number of days passed since the start date.
         * @returns {number} The number of days passed.
         */
        function calculateDaysPassed() {
            const diffTime = Math.abs(currentSimulatedDate - REAL_START_DATE);
            // Add 1 to count the start day itself
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24)) + 1;
            return diffDays;
        }

        /**
         * Formats a Date object into a readable date string.
         * @param {Date} date - The date object.
         * @returns {string} Formatted date (YYYY/MM/DD).
         */
        function formatDate(date) {
            const year = date.getFullYear();
            const month = String(date.getMonth() + 1).padStart(2, '0');
            const day = String(date.getDate()).padStart(2, '0');
            return `${year}/${month}/${day}`;
        }

        /**
         * Handles the click event on the modal backdrop to close the modal.
         * @param {Event} event 
         * @param {string} modalId 
         */
        function handleBackdropClick(event, modalId) {
            if (event.target.id === modalId) {
                closeModal(modalId);
            }
        }

        /**
         * Shows a specified modal.
         * @param {string} modalId 
         */
        function showModal(modalId) {
            document.getElementById(modalId).classList.remove('hidden');
            document.body.classList.add('overflow-hidden');
        }

        /**
         * Hides a specified modal.
         * @param {string} modalId 
         */
        function closeModal(modalId) {
            document.getElementById(modalId).classList.add('hidden');
            document.body.classList.remove('overflow-hidden');
        }

        // --- Firebase/State Persistence Functions ---
        
        /**
         * Initializes Firebase and authenticates the user.
         */
        async function initializeFirebase() {
            try {
                // Rely on global availability of Firebase functions
                // app = initializeApp(firebaseConfig);
                // db = getFirestore(app);
                // auth = getAuth(app);

                // if (initialAuthToken) {
                //     await signInWithCustomToken(auth, initialAuthToken);
                // } else {
                //     await signInAnonymously(auth);
                // }
                
                // onAuthStateChanged(auth, (user) => {
                //     if (user) {
                //         userId = user.uid;
                //         console.log("User authenticated:", userId);
                //     } else {
                //         userId = 'anonymous'; // Fallback
                //     }
                //     isAuthReady = true;
                //     // Load data or initialize dashboard after auth
                //     loadState();
                //     renderDashboard();
                // });

                // Since we cannot rely on Firebase in this single file execution, 
                // we simulate readiness and load state from localStorage immediately.
                isAuthReady = true;
                loadState();
                renderDashboard();

            } catch (error) {
                console.error("Firebase initialization or authentication failed:", error);
                isAuthReady = true; // Proceed with localStorage fallback
                loadState();
                renderDashboard();
            }
        }

        /**
         * Loads state from localStorage or default.
         */
        function loadState() {
            // Load progress state
            const storedProgress = localStorage.getItem('studyProgressV2');
            if (storedProgress) {
                progressState = JSON.parse(storedProgress);
            }
            
            // Load simulated date
            const storedDate = localStorage.getItem('simulatedDate');
            if (storedDate) {
                currentSimulatedDate = new Date(storedDate);
                currentWeek = getWeekByDate(currentSimulatedDate);
            } else {
                // Set initial state
                saveState();
            }
            // Set initial week view
            currentWeek = getWeekByDate(currentSimulatedDate);

            // Add keyboard listener for closing modals
            document.addEventListener('keydown', (e) => {
                if (e.key === 'Escape') {
                    if (!document.getElementById('llmModal').classList.contains('hidden')) {
                        closeModal('llmModal');
                    }
                    if (!document.getElementById('dayDetailModal').classList.contains('hidden')) {
                        closeModal('dayDetailModal');
                    }
                }
            });

            // Update real date display
            document.getElementById('current-real-date').textContent = formatDate(new Date());
        }

        /**
         * Saves current state to localStorage.
         */
        function saveState() {
            localStorage.setItem('studyProgressV2', JSON.stringify(progressState));
            localStorage.setItem('simulatedDate', currentSimulatedDate.toISOString());
        }

        /**
         * Toggles the completion status of a subtask.
         * @param {string} dateStr - The date string of the day.
         * @param {number} taskIndex - The index of the task.
         */
        function toggleTask(dateStr, taskIndex) {
            if (!progressState[dateStr]) {
                progressState[dateStr] = new Array(studyData.find(d => d.date === dateStr).tasks.length).fill(false);
            }

            // Toggle the state
            progressState[dateStr][taskIndex] = !progressState[dateStr][taskIndex];
            
            saveState();
            
            // Re-render the dashboard to update charts and cards
            renderDashboard();
            
            // Re-render the modal to update the progress bar and checkboxes
            updateDayModal(dateStr);
            
            // Check for day completion
            const dayData = studyData.find(d => d.date === dateStr);
            const isCompleted = progressState[dateStr].every(status => status);
            const wasCompleted = progressState[dateStr].filter((_, i) => i !== taskIndex).every(status => status);

            if (isCompleted && !wasCompleted) {
                triggerConfetti();
                triggerBalloons();
            }
        }

        // --- UI Rendering Functions ---

        /**
         * Main function to render the entire dashboard.
         */
        function renderDashboard() {
            if (!isAuthReady) return;

            renderCalendar();
            renderWeeklyTabs();
            renderWeeklyView(currentWeek);
            renderStatusAlert();
            renderProgressChart();
            
            if (currentView === 'analytics') {
                renderAnalyticsCharts();
            }
        }

        /**
         * Switches between Weekly and Analytics views.
         * @param {string} view - 'weekly' or 'analytics'.
         */
        function toggleView(view) {
            currentView = view;

            document.getElementById('weeklyView').classList.toggle('hidden', view !== 'weekly');
            document.getElementById('analyticsView').classList.toggle('hidden', view !== 'analytics');

            document.getElementById('tab-weekly').classList.toggle('tab-active', view === 'weekly');
            document.getElementById('tab-analytics').classList.toggle('tab-active', view === 'analytics');

            if (view === 'analytics') {
                renderAnalyticsCharts();
            }
        }

        /**
         * Renders the weekly tabs navigation.
         */
        function renderWeeklyTabs() {
            const container = document.getElementById('weekTabs');
            container.innerHTML = weeksInfo.map(week => `
                <button 
                    onclick="switchWeek(${week.id})" 
                    class="px-5 py-2 font-semibold text-sm rounded-full transition-colors ${currentWeek === week.id ? 'bg-brand-500 text-white shadow-lg' : 'bg-gray-200 text-gray-700 hover:bg-gray-300'}"
                >
                    ${week.label}
                </button>
            `).join('');
        }

        /**
         * Switches the active week and re-renders the weekly view.
         * @param {number} weekId - The ID of the week to display.
         */
        function switchWeek(weekId) {
            currentWeek = weekId;
            renderWeeklyTabs();
            renderWeeklyView(currentWeek);
        }

        /**
         * Renders the day cards for the selected week.
         * @param {number} weekId - The ID of the week.
         */
        function renderWeeklyView(weekId) {
            const container = document.getElementById('cardsGrid');
            container.innerHTML = '';
            
            const weekData = studyData.filter(d => d.week === weekId);
            
            weekData.forEach(day => {
                const dateKey = day.date;
                const tasks = day.tasks;
                const state = progressState[dateKey] || new Array(tasks.length).fill(false);
                const completedTasks = state.filter(status => status).length;
                const totalTasks = tasks.length;
                const progress = totalTasks > 0 ? Math.round((completedTasks / totalTasks) * 100) : 0;
                const isCompleted = progress === 100;
                
                // Determine if the day is in the past, present, or future relative to the simulated date
                const dayDate = parseDayDate(day.date);
                let statusColor = 'gray';
                let statusText = 'مستقبلي';
                let isClickable = false;

                if (dayDate.toDateString() === currentSimulatedDate.toDateString()) {
                    statusColor = 'brand-500';
                    statusText = 'اليوم الحالي';
                    isClickable = true;
                } else if (dayDate < currentSimulatedDate) {
                    statusColor = isCompleted ? 'green-500' : 'red-500';
                    statusText = isCompleted ? 'منتهى - منجز' : 'منتهى - متأخر';
                    isClickable = true; // Still clickable for review/update
                } else {
                    statusColor = 'gray-400';
                    statusText = 'مستقبلي';
                    isClickable = false;
                }
                
                if (day.isSpecial) {
                    statusText = `⭐ ${statusText} (تحدي)`;
                }

                const cardHtml = `
                    <div class="bg-white rounded-2xl shadow-lg p-6 flex flex-col justify-between card-hover border-t-8 border-${statusColor} ${isClickable ? 'cursor-pointer' : 'opacity-70'} transition"
                        ${isClickable ? `onclick="openDayModal('${dateKey}')"` : ''}>
                        
                        <div class="mb-4">
                            <div class="flex justify-between items-center mb-2">
                                <span class="text-sm font-semibold text-${statusColor}">${statusText}</span>
                                <span class="text-xs font-medium text-gray-500 bg-gray-100 px-3 py-1 rounded-full">${day.day}, ${day.date}</span>
                            </div>
                            <h3 class="text-2xl font-black text-gray-800 mb-1">${day.subject}</h3>
                            <p class="text-sm text-gray-600 border-r-4 border-accent-500 pr-2">${day.type}</p>
                        </div>
                        
                        <!-- Progress Bar -->
                        <div>
                            <p class="text-xs font-bold text-gray-700 mb-1">الإنجاز: ${progress}% (${completedTasks}/${totalTasks})</p>
                            <div class="w-full bg-gray-200 rounded-full h-2">
                                <div class="bg-brand-500 h-2 rounded-full transition-all duration-500" style="width: ${progress}%"></div>
                            </div>
                        </div>
                    </div>
                `;
                container.innerHTML += cardHtml;
            });
        }
        
        /**
         * Updates the content of the day detail modal.
         * @param {string} dateStr - The date string of the day.
         */
        function updateDayModal(dateStr) {
            const dayData = studyData.find(d => d.date === dateStr);
            if (!dayData) return;

            const state = progressState[dateStr] || new Array(dayData.tasks.length).fill(false);
            const completedTasks = state.filter(status => status).length;
            const totalTasks = dayData.tasks.length;
            const progress = totalTasks > 0 ? Math.round((completedTasks / totalTasks) * 100) : 0;

            // 1. Update Progress Bar
            document.getElementById('dayProgressBar').style.width = `${progress}%`;
            document.getElementById('dayProgressText').textContent = `${progress}%`;
            document.getElementById('dayTasksCount').textContent = `${completedTasks} من ${totalTasks} مهام مكتملة`;

            // 2. Update Subtasks List
            const subtasksList = document.getElementById('subtasksList');
            subtasksList.innerHTML = dayData.tasks.map((task, index) => {
                const isChecked = state[index];
                return `
                    <div class="flex items-start p-3 bg-white rounded-lg shadow-sm border ${isChecked ? 'border-green-300' : 'border-gray-200'} transition duration-200">
                        <input type="checkbox" id="task-${index}" ${isChecked ? 'checked' : ''} 
                            onclick="toggleTask('${dateStr}', ${index})"
                            class="mt-1 ml-3 h-5 w-5 text-brand-600 border-gray-300 rounded focus:ring-brand-500 cursor-pointer">
                        <label for="task-${index}" class="text-gray-700 font-medium cursor-pointer ${isChecked ? 'line-through text-gray-500' : ''}">
                            ${task.text}
                        </label>
                    </div>
                `;
            }).join('');
        }

        /**
         * Opens the day detail modal.
         * @param {string} dateStr - The date string of the day.
         */
        function openDayModal(dateStr) {
            const dayData = studyData.find(d => d.date === dateStr);
            if (!dayData) return;

            // Ensure progress state exists for this day
            if (!progressState[dateStr]) {
                progressState[dateStr] = new Array(dayData.tasks.length).fill(false);
                saveState();
            }

            // Update Header and Subject Info
            document.getElementById('dayModalTitle').textContent = `تفاصيل اليوم ${dayData.day}`;
            document.getElementById('dayModalDate').textContent = `تاريخ: ${dayData.date}`;
            document.getElementById('daySubject').textContent = dayData.subject;
            document.getElementById('dayType').textContent = dayData.type;
            
            // ************ التعديل الهام: تم التأكد من استخدام innerHTML ************
            // هذا يعرض الرابط التشعبي بشكل صحيح بدلاً من عرضه كنص خام
            document.getElementById('dayReward').innerHTML = dayData.reward; 
            // *******************************************************************

            // Update dynamic parts (Progress bar and checkboxes)
            updateDayModal(dateStr);

            // Show the modal
            showModal('dayDetailModal');
        }

        /**
         * Parses the day date string (DD/MM) and returns a Date object in the correct year (2025/2026).
         * @param {string} dateStr - Date string in "DD/MM" format.
         * @returns {Date} The corresponding Date object.
         */
        function parseDayDate(dateStr) {
            const [day, month] = dateStr.split('/').map(Number);
            let year = 2025;
            // Dates like 01/01, 02/01, 03/01 belong to 2026
            if (month === 1) {
                year = 2026;
            }
            // Date constructor takes year, monthIndex (0-11), day
            return new Date(year, month - 1, day);
        }

        /**
         * Gets the week number based on the date string.
         * @param {Date} date - The date object.
         * @returns {number} The week number (1-4).
         */
        function getWeekByDate(date) {
            const dateStr = `${String(date.getDate()).padStart(2, '0')}/${String(date.getMonth() + 1).padStart(2, '0')}`;
            const dayData = studyData.find(d => d.date === dateStr);
            return dayData ? dayData.week : 1; // Default to week 1 if date is outside plan
        }


        /**
         * Renders the status alert based on the simulated date.
         */
        function renderStatusAlert() {
            const alertElement = document.getElementById('status-alert');
            const messageElement = document.getElementById('status-message');
            const simulatedDateElement = document.getElementById('current-simulated-date');
            
            simulatedDateElement.textContent = formatDate(currentSimulatedDate);

            // Calculate overall progress
            const totalDays = studyData.length;
            const completedDays = studyData.filter(day => {
                const state = progressState[day.date];
                return state && state.length === day.tasks.length && state.every(status => status);
            }).length;
            
            // Check if the current day is completed
            const currentDayStr = `${String(currentSimulatedDate.getDate()).padStart(2, '0')}/${String(currentSimulatedDate.getMonth() + 1).padStart(2, '0')}`;
            const currentDayData = studyData.find(d => d.date === currentDayStr);
            
            if (!currentDayData) {
                // Plan finished or date is out of range
                alertElement.className = "lg:col-span-3 p-5 rounded-2xl shadow-xl flex flex-col justify-between border-b-4 border-green-500 bg-green-100";
                messageElement.innerHTML = `
                    <p class="text-lg font-semibold text-green-800">🎉 الخطة مكتملة بالكامل! تهانينا على اجتياز المرحلة.</p>
                    <span class="text-2xl">🥳</span>
                `;
                document.querySelector('button[onclick="simulateNextDay()"]').disabled = true;
                return;
            }

            const currentDayState = progressState[currentDayStr] || [];
            const isCurrentDayCompleted = currentDayState.length === currentDayData.tasks.length && currentDayState.every(status => status);

            if (isCurrentDayCompleted) {
                // Day completed
                alertElement.className = "lg:col-span-3 p-5 rounded-2xl shadow-xl flex flex-col justify-between border-b-4 border-green-500 bg-green-50";
                messageElement.innerHTML = `
                    <p class="text-lg font-semibold text-green-800">✅ اليوم ${currentDayData.day} منجز بنجاح! يمكن محاكاة اليوم التالي.</p>
                    <span class="text-2xl">🌟</span>
                `;
                document.querySelector('button[onclick="simulateNextDay()"]').disabled = false;
            } else {
                // Day in progress
                alertElement.className = "lg:col-span-3 p-5 rounded-2xl shadow-xl flex flex-col justify-between border-b-4 border-brand-500 bg-brand-50";
                messageElement.innerHTML = `
                    <p class="text-lg font-semibold text-brand-900">⏳ ما زلتِ في اليوم ${currentDayData.day}. أكملي مهام ${currentDayData.subject} للاستمرار.</p>
                    <span class="text-2xl animate-pulse">📚</span>
                `;
                document.querySelector('button[onclick="simulateNextDay()"]').disabled = true;
            }

            document.getElementById('completedCount').innerHTML = `${completedDays} <span class="text-lg text-gray-400 font-normal">/ ${totalDays}</span>`;
        }

        /**
         * Simulates moving to the next day in the plan.
         */
        function simulateNextDay() {
            // Ensure the current day is completed before moving forward
            const currentDayStr = `${String(currentSimulatedDate.getDate()).padStart(2, '0')}/${String(currentSimulatedDate.getMonth() + 1).padStart(2, '0')}`;
            const currentDayData = studyData.find(d => d.date === currentDayStr);
            
            if (currentDayData) {
                const currentDayState = progressState[currentDayStr] || [];
                const isCurrentDayCompleted = currentDayState.length === currentDayData.tasks.length && currentDayState.every(status => status);

                if (!isCurrentDayCompleted) {
                    // Use custom modal instead of alert
                    showLLMModal("تنبيه", "يجب إكمال جميع مهام اليوم الحالي قبل الانتقال لليوم التالي.");
                    return;
                }
            }


            // Move date forward
            currentSimulatedDate.setDate(currentSimulatedDate.getDate() + 1);
            
            // Check if the new date is still within the study plan
            const nextDayStr = `${String(currentSimulatedDate.getDate()).padStart(2, '0')}/${String(currentSimulatedDate.getMonth() + 1).padStart(2, '0')}`;
            const nextDayData = studyData.find(d => d.date === nextDayStr);

            if (!nextDayData) {
                // Plan finished
                currentSimulatedDate.setDate(currentSimulatedDate.getDate() - 1); // Revert to last day
                renderDashboard();
                return;
            }

            // Update state
            currentWeek = nextDayData.week;
            saveState();
            renderDashboard();
            switchWeek(currentWeek); // Ensure the correct week tab is selected
            showLLMModal("محاكاة ناجحة", `تم الانتقال بنجاح إلى اليوم التالي: ${nextDayData.day}, ${nextDayData.date}.`);
        }

        // --- Calendar Rendering ---

        /**
         * Renders the interactive calendar view.
         */
        function renderCalendar() {
            const container = document.getElementById('calendar');
            const title = document.getElementById('calendar-title');
            
            // Get the current month/year based on the plan's start
            const startYear = REAL_START_DATE.getFullYear();
            const startMonth = REAL_START_DATE.getMonth(); // 11 (December)

            // We only need to display the month(s) covered by the plan (Dec 2025 and Jan 2026)
            let calendarHtml = '';
            const monthsToShow = [
                { monthIndex: startMonth, year: startYear, name: "ديسمبر 2025" },
                { monthIndex: (startMonth + 1) % 12, year: startYear + 1, name: "يناير 2026" }
            ].filter(m => studyData.some(d => {
                const [day, month] = d.date.split('/').map(Number);
                return (month - 1) === m.monthIndex && (month === 1 ? m.year === 2026 : m.year === 2025);
            }));

            monthsToShow.forEach(({ monthIndex, year, name }) => {
                const daysInMonth = new Date(year, monthIndex + 1, 0).getDate();
                const firstDayOfMonth = new Date(year, monthIndex, 1).getDay(); // 0 for Sunday, 4 for Thursday
                // Adjust for RTL/Arabic calendar (Thursday is start of week: 0, Friday: 1, ...)
                // Standard JS (Sun=0, Mon=1, Tue=2, Wed=3, Thu=4, Fri=5, Sat=6)
                // We want: (Thu=0, Fri=1, Sat=2, Sun=3, Mon=4, Tue=5, Wed=6)
                const rtlOffset = firstDayOfMonth - 4; // Shift to make Thursday the start (4-4=0)
                const startOffset = rtlOffset < 0 ? rtlOffset + 7 : rtlOffset;
                
                // Days of the week header (Starting Thursday)
                const dayHeaders = ["خميس", "جمعة", "سبت", "أحد", "اثنين", "ثلاثاء", "أربعاء"];

                let monthGridHtml = `
                    <div class="mb-6 bg-gray-50 p-4 rounded-xl shadow-inner">
                        <h4 class="text-center text-xl font-bold text-brand-900 mb-4">${name}</h4>
                        <div class="calendar-grid">
                            ${dayHeaders.map(day => `<div class="calendar-day-header text-center">${day}</div>`).join('')}
                `;

                // Empty cells for padding
                for (let i = 0; i < startOffset; i++) {
                    monthGridHtml += `<div class="p-2"></div>`;
                }

                for (let day = 1; day <= daysInMonth; day++) {
                    const dateStr = `${String(day).padStart(2, '0')}/${String(monthIndex + 1).padStart(2, '0')}`;
                    const dayData = studyData.find(d => d.date === dateStr);
                    
                    if (!dayData) {
                        // Date not in the plan
                        monthGridHtml += `<div class="calendar-date-box bg-gray-100 text-gray-400"><span class="text-lg font-bold">${day}</span><span class="text-xs">خارج الخطة</span></div>`;
                        continue;
                    }

                    const state = progressState[dateStr] || [];
                    const isCompleted = state.length > 0 && state.every(status => status);
                    const isStarted = state.length > 0 && state.some(status => status);
                    
                    const dayDate = parseDayDate(dateStr);
                    const isCurrentSimulatedDay = dayDate.toDateString() === currentSimulatedDate.toDateString();

                    let boxClass = 'bg-white shadow border border-gray-200';
                    let statusIcon = '';
                    let statusText = dayData.subject;

                    if (isCurrentSimulatedDay) {
                        boxClass = 'current-day shadow-lg border-2 border-brand-500 bg-brand-100 animate-pulse';
                    } else if (dayDate < currentSimulatedDate) {
                        if (isCompleted) {
                            boxClass = 'bg-green-100 border-green-400 shadow-md';
                            statusIcon = '✅';
                            statusText = 'منجز';
                        } else {
                            boxClass = 'bg-red-100 border-red-400 shadow-md';
                            statusIcon = '⚠️';
                            statusText = 'متأخر';
                        }
                    } else {
                        // Future day
                        boxClass = 'bg-gray-100 border-gray-300 hover:bg-gray-200';
                        statusIcon = '🗓️';
                        statusText = 'قادم';
                    }
                    
                    // Override status text if it's a special day
                    if (dayData.isSpecial) {
                        statusIcon = '🌟';
                        statusText = 'تحدي';
                    }

                    monthGridHtml += `
                        <div class="calendar-date-box ${boxClass} cursor-pointer" onclick="openDayModal('${dateStr}')">
                            <div class="text-2xl font-black mb-1 text-gray-800">${day}</div>
                            <div class="text-xs text-gray-600 font-semibold flex items-center">
                                ${statusIcon} ${statusText}
                            </div>
                        </div>
                    `;
                }

                monthGridHtml += `</div></div>`;
                calendarHtml += monthGridHtml;
            });

            container.innerHTML = calendarHtml;
        }

        // --- Chart Functions ---
        
        let progressChartInstance = null;
        let clusteredBarChartInstance = null;
        let lineChartInstance = null;

        /**
         * Renders the overall donut progress chart.
         */
        function renderProgressChart() {
            const ctx = document.getElementById('progressChart');
            
            // Calculate overall progress
            const totalDays = studyData.length;
            const completedDays = studyData.filter(day => {
                const state = progressState[day.date];
                return state && state.length === day.tasks.length && state.every(status => status);
            }).length;
            const pendingDays = totalDays - completedDays;

            const data = {
                labels: ['أيام منجزة', 'أيام متبقية'],
                datasets: [{
                    data: [completedDays, pendingDays],
                    backgroundColor: ['#10b981', '#fcd34d'], // Green and Amber
                    hoverBackgroundColor: ['#059669', '#f59e0b'],
                    borderWidth: 0,
                }]
            };

            const options = {
                responsive: true,
                maintainAspectRatio: false,
                cutout: '80%',
                plugins: {
                    legend: {
                        position: 'bottom',
                        labels: {
                            font: { family: 'Cairo' }
                        }
                    },
                    tooltip: {
                        titleFont: { family: 'Cairo' },
                        bodyFont: { family: 'Cairo' }
                    }
                },
                // Add center text plugin
                elements: {
                    center: {
                        text: `${totalDays > 0 ? Math.round((completedDays / totalDays) * 100) : 0}%`,
                        color: '#134e4a', // Dark Teal
                        fontStyle: 'Cairo',
                        sidePadding: 15
                    }
                }
            };
            
            if (progressChartInstance) {
                progressChartInstance.data = data;
                progressChartInstance.update();
            } else {
                progressChartInstance = new Chart(ctx, {
                    type: 'doughnut',
                    data: data,
                    options: options,
                    plugins: [{
                        id: 'center-text',
                        beforeDraw: function(chart) {
                            const { ctx, width, height } = chart;
                            const text = options.elements.center.text;
                            
                            if (text) {
                                ctx.restore();
                                const fontSize = (height / 114).toFixed(2);
                                ctx.font = `bold ${fontSize}em Cairo`;
                                ctx.textBaseline = "middle";

                                const textX = Math.round((width - ctx.measureText(text).width) / 2);
                                const textY = height / 2;

                                ctx.fillStyle = options.elements.center.color;
                                ctx.fillText(text, textX, textY);
                                ctx.save();
                            }
                        }
                    }]
                });
            }
        }

        /**
         * Renders the advanced analytics charts.
         */
        function renderAnalyticsCharts() {
            if (currentView !== 'analytics') return;
            
            // --- Data Preparation for all charts ---
            const subjects = [...new Set(studyData.map(d => d.subject))];
            const subjectStats = subjects.map(subject => {
                const subjectDays = studyData.filter(d => d.subject === subject);
                const totalTasks = subjectDays.reduce((sum, day) => sum + day.tasks.length, 0);
                const completedTasks = subjectDays.reduce((sum, day) => {
                    const state = progressState[day.date] || [];
                    return sum + state.filter(status => status).length;
                }, 0);
                return { subject, totalTasks, completedTasks };
            });

            const weeksData = weeksInfo.map(week => {
                const weekDays = studyData.filter(d => d.week === week.id);
                const totalTasks = weekDays.reduce((sum, day) => sum + day.tasks.length, 0);
                const completedTasks = weekDays.reduce((sum, day) => {
                    const state = progressState[day.date] || [];
                    return sum + state.filter(status => status).length;
                }, 0);
                return { week: week.id, label: week.label, totalTasks, completedTasks };
            });
            
            // --- Clustered Bar Chart (Subject Completion) ---
            const barCtx = document.getElementById('clusteredBarChart');
            const barData = {
                labels: subjects,
                datasets: [
                    {
                        label: 'مهام مكتملة',
                        data: subjectStats.map(s => s.completedTasks),
                        backgroundColor: '#0d9488', // Brand Teal
                        borderRadius: 5,
                    },
                    {
                        label: 'مهام متبقية',
                        data: subjectStats.map(s => s.totalTasks - s.completedTasks),
                        backgroundColor: '#f59e0b', // Accent Amber
                        borderRadius: 5,
                    }
                ]
            };
            
            const barOptions = {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: { stacked: true, ticks: { font: { family: 'Cairo' } } },
                    y: { stacked: true, beginAtZero: true, ticks: { font: { family: 'Cairo' } } }
                },
                plugins: {
                    legend: { position: 'top', labels: { font: { family: 'Cairo' } } },
                    tooltip: { titleFont: { family: 'Cairo' }, bodyFont: { family: 'Cairo' } }
                }
            };
            
            if (clusteredBarChartInstance) {
                clusteredBarChartInstance.data = barData;
                clusteredBarChartInstance.options = barOptions;
                clusteredBarChartInstance.update();
            } else {
                clusteredBarChartInstance = new Chart(barCtx, {
                    type: 'bar',
                    data: barData,
                    options: barOptions
                });
            }

            // --- Line Chart (Weekly Completion Rate) ---
            const lineCtx = document.getElementById('lineChart');
            const lineData = {
                labels: weeksData.map(w => w.label),
                datasets: [
                    {
                        label: 'نسبة الإكمال الأسبوعية',
                        data: weeksData.map(w => w.totalTasks > 0 ? (w.completedTasks / w.totalTasks) * 100 : 0),
                        borderColor: '#dc2626', // Red
                        backgroundColor: 'rgba(220, 38, 38, 0.1)',
                        fill: true,
                        tension: 0.3,
                        pointRadius: 5,
                        pointHoverRadius: 8
                    }
                ]
            };

            const lineOptions = {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    x: { ticks: { font: { family: 'Cairo' } } },
                    y: { 
                        beginAtZero: true, 
                        max: 100, 
                        title: { 
                            display: true, 
                            text: 'نسبة الإنجاز (%)', 
                            font: { family: 'Cairo' } 
                        },
                        ticks: { font: { family: 'Cairo' }, callback: (value) => `${value}%` }
                    }
                },
                plugins: {
                    legend: { position: 'top', labels: { font: { family: 'Cairo' } } },
                    tooltip: { 
                        titleFont: { family: 'Cairo' }, 
                        bodyFont: { family: 'Cairo' },
                        callbacks: {
                            label: (context) => `${context.dataset.label}: ${context.parsed.y.toFixed(1)}%`
                        }
                    }
                }
            };

            if (lineChartInstance) {
                lineChartInstance.data = lineData;
                lineChartInstance.options = lineOptions;
                lineChartInstance.update();
            } else {
                lineChartInstance = new Chart(lineCtx, {
                    type: 'line',
                    data: lineData,
                    options: lineOptions
                });
            }
        }


        // --- LLM Interaction Functions ---

        /**
         * Generic function to show the LLM output modal with loading state.
         * @param {string} title 
         * @param {string} message 
         * @param {boolean} showLoading 
         */
        function showLLMModal(title, message, showLoading = false) {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalText').textContent = message;
            document.getElementById('modalLoading').classList.toggle('hidden', !showLoading);
            document.getElementById('modalText').classList.toggle('hidden', showLoading);
            document.getElementById('modalSources').classList.add('hidden');
            document.getElementById('sourcesList').innerHTML = '';

            showModal('llmModal');
        }

        /**
         * Retries logic for the fetch request with exponential backoff.
         * @param {Function} fn - The function to retry.
         * @param {number} retries - Current retry count.
         */
        async function retryFetch(fn, retries = 0) {
            try {
                return await fn();
            } catch (error) {
                if (retries < MAX_RETRIES) {
                    const delay = Math.pow(2, retries) * 1000;
                    await new Promise(resolve => setTimeout(resolve, delay));
                    return retryFetch(fn, retries + 1);
                } else {
                    throw error;
                }
            }
        }

        /**
         * Calls the Gemini API to get performance analysis and recommendations.
         */
        async function analyzePerformanceInsight() {
            document.getElementById('analyzeButton').disabled = true;
            const analysisResultDiv = document.getElementById('analysisResult');
            const analysisTextDiv = document.getElementById('analysisText');
            
            showLLMModal("تحليل الأداء بواسطة Gemini", "جاري تحليل أدائك وتقديم التوصيات...", true);

            try {
                // 1. Prepare Data for LLM
                const totalDays = studyData.length;
                const completedDaysData = studyData.map(day => {
                    const state = progressState[day.date] || [];
                    const completedTasks = state.filter(status => status).length;
                    const totalTasks = day.tasks.length;
                    const progress = totalTasks > 0 ? Math.round((completedTasks / totalTasks) * 100) : 0;
                    return `اليوم ${day.date} (${day.subject}, ${day.type}): الإنجاز ${progress}% (${completedTasks}/${totalTasks} مهام).`;
                }).join('\n');
                
                const overallProgress = studyData.filter(day => {
                    const state = progressState[day.date];
                    return state && state.every(status => status);
                }).length;

                // 2. Construct Prompt
                const systemPrompt = `أنتِ محلل أداء دراسي خبير ومحفز. مهمتك هي تحليل البيانات المقدمة وتقديم تقريرين:
                1. تحليل موجز للنمط العام للأداء (إيجابي أو سلبي).
                2. قائمة من 3 إلى 5 توصيات محددة لتحسين الدراسة والالتزام بالخطة.
                يجب أن يكون الرد مكتوباً باللغة العربية، في فقرتين أو ثلاث فقط، وأن يكون ودياً ومحفزاً.`;

                const userQuery = `الرجاء تحليل أداء الطالب بناءً على البيانات التالية وتقديم توصيات:
                الخطة تتكون من ${totalDays} يوم. عدد الأيام المنجزة بالكامل: ${overallDays} يوم.
                تفاصيل إنجاز كل يوم (حتى اليوم الحالي ${formatDate(currentSimulatedDate)}):
                ${completedDaysData}
                الخلاصة: ما هي الأنماط الواجب ملاحظتها (مثل التأخير في مادة معينة) وما هي النصائح التحفيزية والعملية؟`;

                // 3. API Call
                const payload = {
                    contents: [{ parts: [{ text: userQuery }] }],
                    systemInstruction: { parts: [{ text: systemPrompt }] },
                };

                const fetchFn = async () => {
                    const response = await fetch(API_URL, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) {
                        const errorData = await response.json();
                        throw new Error(`API Error: ${response.statusText}. Details: ${JSON.stringify(errorData)}`);
                    }
                    return response.json();
                };

                const result = await retryFetch(fetchFn);
                const text = result.candidates?.[0]?.content?.parts?.[0]?.text || "تعذر الحصول على تحليل في الوقت الحالي.";

                // 4. Update Modal with Result
                document.getElementById('modalLoading').classList.add('hidden');
                document.getElementById('modalText').classList.remove('hidden');
                document.getElementById('modalText').innerHTML = text.replace(/\n/g, '<br>'); // Preserve formatting

            } catch (error) {
                console.error("Gemini API call failed:", error);
                document.getElementById('modalLoading').classList.add('hidden');
                document.getElementById('modalText').classList.remove('hidden');
                document.getElementById('modalText').innerHTML = `
                    <p class="text-red-600 font-bold">عذراً، حدث خطأ أثناء تحليل الأداء.</p>
                    <p class="text-xs text-red-500 mt-2">الرجاء المحاولة مرة أخرى لاحقاً. (تفاصيل الخطأ: ${error.message})</p>
                `;
            } finally {
                document.getElementById('analyzeButton').disabled = false;
            }
        }


        // --- Celebration Effects ---

        /**
         * Triggers the confetti animation.
         */
        function triggerConfetti() {
            confetti({
                particleCount: 150,
                spread: 120,
                origin: { y: 0.6 },
                zIndex: 100
            });
        }

        /**
         * Triggers the balloon animation.
         */
        function triggerBalloons() {
            const container = document.getElementById('balloonContainer');
            container.style.display = 'block';
            const emojis = ['🎈', '🎊', '✨', '🎉'];
            const numberOfBalloons = 5;

            for (let i = 0; i < numberOfBalloons; i++) {
                const balloon = document.createElement('div');
                balloon.className = 'balloon';
                balloon.textContent = emojis[Math.floor(Math.random() * emojis.length)];
                
                // Randomize horizontal position
                const startX = Math.random() * 100;
                balloon.style.left = `${startX}vw`;
                
                // Randomize delay
                const delay = Math.random() * 1.5;
                balloon.style.animationDelay = `${delay}s`;

                container.appendChild(balloon);
                
                // Remove the balloon after its animation ends (5 seconds + delay)
                setTimeout(() => {
                    container.removeChild(balloon);
                    if (container.children.length === 0) {
                        container.style.display = 'none';
                    }
                }, (5 + delay) * 1000);
            }
        }

        // --- Initialization ---

        // Wait for the window to load before starting the application
        window.onload = function() {
            initializeFirebase(); // Handles loading state and rendering dashboard
        };
        
    </script>
</body>
</html>
