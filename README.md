<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>سحابتي بلس | Cloud Storage</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cairo:wght@300;400;700;900&display=swap');
        body { 
            font-family: 'Cairo', sans-serif; 
            background-color: #f8fafc;
            -webkit-tap-highlight-color: transparent;
        }
        .glass { background: rgba(255, 255, 255, 0.9); backdrop-filter: blur(12px); }
        .admin-gradient { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); }
        /* تحسين مظهر التمرير على الجوال */
        ::-webkit-scrollbar { width: 4px; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body class="text-slate-900 overflow-x-hidden">

    <!-- Loading Overlay -->
    <div id="loadingOverlay" class="fixed inset-0 bg-white/90 z-[100] flex items-center justify-center hidden">
        <div class="flex flex-col items-center">
            <div class="animate-spin rounded-full h-12 w-12 border-t-4 border-blue-600 mb-4"></div>
            <p class="text-slate-600 font-bold">جاري المعالجة...</p>
        </div>
    </div>

    <!-- Navigation -->
    <nav class="glass border-b sticky top-0 z-50 px-4 md:px-6 py-3 flex justify-between items-center">
        <div class="flex items-center gap-2">
            <div class="bg-blue-600 p-2 rounded-lg text-white shadow-md">
                <i class="fas fa-cloud"></i>
            </div>
            <span class="text-lg font-black text-blue-800 tracking-tight">سحابتي</span>
        </div>
        <div id="userInfo" class="hidden flex items-center gap-3">
            <div class="hidden sm:block text-left">
                <p id="userRole" class="text-[9px] font-bold text-blue-600 uppercase"></p>
                <p id="userDisplayEmail" class="text-xs text-slate-500"></p>
            </div>
            <button onclick="handleLogout()" class="bg-red-50 text-red-500 p-2 rounded-lg hover:bg-red-100 transition-all">
                <i class="fas fa-sign-out-alt"></i>
            </button>
        </div>
    </nav>

    <main class="max-w-6xl mx-auto p-4 md:p-6">
        
        <!-- Login Screen -->
        <section id="loginSection" class="max-w-md mx-auto mt-10 md:mt-20 bg-white p-6 md:p-10 rounded-[2.5rem] shadow-xl border border-slate-100">
            <div class="text-center mb-8">
                <h2 class="text-2xl md:text-3xl font-black text-slate-800">سجل دخولك</h2>
                <p class="text-slate-400 mt-2 text-sm">مساحتك الآمنة بانتظارك</p>
            </div>
            <div class="space-y-4">
                <div class="space-y-1">
                    <label class="text-xs font-bold text-slate-500 px-1">البريد الإلكتروني</label>
                    <input id="loginEmail" type="email" class="w-full px-5 py-4 bg-slate-50 border-2 border-transparent focus:border-blue-500 rounded-2xl outline-none transition-all" placeholder="name@example.com">
                </div>
                <button onclick="showOtp()" class="w-full bg-blue-600 text-white py-4 rounded-2xl font-black shadow-lg shadow-blue-100 hover:scale-[1.02] active:scale-95 transition-all">
                    استمرار
                </button>
            </div>
        </section>

        <!-- OTP Screen -->
        <section id="otpSection" class="max-w-md mx-auto mt-10 md:mt-20 bg-white p-8 rounded-[2.5rem] shadow-xl border border-slate-100 text-center hidden">
            <h2 class="text-2xl font-black text-slate-800 mb-2">رمز التحقق</h2>
            <p class="text-slate-400 text-sm mb-6">أدخل الرمز (123456)</p>
            <div class="space-y-4">
                <input id="otpCode" type="text" maxLength="6" class="w-full text-center text-3xl font-black tracking-widest py-4 bg-slate-50 rounded-2xl outline-none" placeholder="000000">
                <p id="otpError" class="text-red-500 text-xs font-bold hidden">الرمز غير صحيح</p>
                <button onclick="handleLogin()" class="w-full bg-indigo-600 text-white py-4 rounded-2xl font-black transition-all active:scale-95">
                    تأكيد الدخول
                </button>
            </div>
        </section>

        <!-- Dashboard -->
        <section id="dashboardSection" class="space-y-6 hidden">
            <!-- Tabs (Mobile Optimized) -->
            <div id="adminTabs" class="flex bg-slate-200 p-1 rounded-2xl w-full md:w-fit mx-auto hidden">
                <button onclick="switchTab('my-files')" id="tabMyFiles" class="flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all bg-white shadow text-blue-600">ملفاتي</button>
                <button onclick="switchTab('admin')" id="tabAdmin" class="flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all text-slate-500">الإدارة</button>
            </div>

            <!-- Content -->
            <div id="myFilesContent" class="space-y-6">
                <!-- Upload Card -->
                <div class="bg-white p-5 md:p-8 rounded-[2rem] shadow-sm border border-slate-100">
                    <div class="flex items-center justify-between mb-6">
                        <h3 class="font-black text-slate-800 flex items-center gap-2">
                            <i class="fas fa-plus-circle text-blue-500"></i> إضافة محتوى
                        </h3>
                        <!-- System File Picker Button -->
                        <button onclick="document.getElementById('systemFile').click()" class="text-xs font-bold bg-blue-50 text-blue-600 px-3 py-2 rounded-lg hover:bg-blue-100">
                            <i class="fas fa-paperclip ml-1"></i> اختر من الجهاز
                        </button>
                        <input type="file" id="systemFile" class="hidden" onchange="handleFileSelect(event)">
                    </div>
                    
                    <!-- File Type Selector (Scrollable on Mobile) -->
                    <div class="flex overflow-x-auto pb-2 gap-3 no-scrollbar">
                        <button onclick="setFileType('text')" class="file-type-btn flex-shrink-0 px-4 py-3 rounded-xl border-2 border-blue-500 bg-blue-50 flex items-center gap-2" data-type="text">
                            <i class="fas fa-file-alt text-slate-500"></i><span class="text-xs font-bold">نص</span>
                        </button>
                        <button onclick="setFileType('image')" class="file-type-btn flex-shrink-0 px-4 py-3 rounded-xl border-2 border-slate-50 bg-slate-50 flex items-center gap-2" data-type="image">
                            <i class="fas fa-image text-blue-500"></i><span class="text-xs font-bold">صورة</span>
                        </button>
                        <button onclick="setFileType('video')" class="file-type-btn flex-shrink-0 px-4 py-3 rounded-xl border-2 border-slate-50 bg-slate-50 flex items-center gap-2" data-type="video">
                            <i class="fas fa-video text-red-500"></i><span class="text-xs font-bold">فيديو</span>
                        </button>
                        <button onclick="setFileType('audio')" class="file-type-btn flex-shrink-0 px-4 py-3 rounded-xl border-2 border-slate-50 bg-slate-50 flex items-center gap-2" data-type="audio">
                            <i class="fas fa-music text-green-500"></i><span class="text-xs font-bold">صوت</span>
                        </button>
                    </div>

                    <div class="mt-6 space-y-3">
                        <input id="fileName" type="text" class="w-full px-4 py-3 bg-slate-50 rounded-xl outline-none focus:ring-2 focus:ring-blue-500 text-sm font-bold" placeholder="عنوان الملف...">
                        <textarea id="fileContent" class="w-full px-4 py-3 bg-slate-50 rounded-xl outline-none focus:ring-2 focus:ring-blue-500 text-sm min-h-[100px]" placeholder="اكتب ملاحظة أو الصق رابطاً..."></textarea>
                        <button id="uploadBtn" onclick="uploadFile()" class="w-full bg-slate-900 text-white py-4 rounded-2xl font-black text-sm active:scale-95 transition-all">رفع إلى السحابة</button>
                    </div>
                </div>

                <!-- Grid -->
                <div id="filesGrid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                    <!-- Files -->
                </div>
            </div>

            <!-- Admin -->
            <div id="adminContent" class="admin-gradient text-white rounded-[2rem] p-5 md:p-8 shadow-2xl hidden overflow-hidden">
                <h3 class="text-xl font-black mb-6">السجل العالمي</h3>
                <div class="overflow-x-auto -mx-5 md:mx-0">
                    <table class="w-full text-right text-xs">
                        <thead class="text-slate-500 border-b border-slate-800">
                            <tr>
                                <th class="p-4">المستخدم</th>
                                <th class="p-4">الملف</th>
                                <th class="p-4 text-left">التاريخ</th>
                            </tr>
                        </thead>
                        <tbody id="adminTableBody" class="divide-y divide-slate-800">
                        </tbody>
                    </table>
                </div>
            </div>
        </section>
    </main>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc, query } from "https://www.gstatic.com/firebasejs/11.0.1/firebase-firestore.js";

        const firebaseConfig = JSON.parse(__firebase_config);
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'cloud-storage-final';
        const ADMIN_EMAIL = "y0111096464@gmail.com";

        let currentUser = null;
        let currentEmail = localStorage.getItem('cloud_email') || "";
        let currentFileType = "text";
        let unsubs = [];

        // UI Handlers
        window.showOtp = () => {
            currentEmail = document.getElementById('loginEmail').value;
            if(!currentEmail.includes('@')) return alert('البريد غير صحيح');
            localStorage.setItem('cloud_email', currentEmail);
            document.getElementById('loginSection').classList.add('hidden');
            document.getElementById('otpSection').classList.remove('hidden');
        };

        window.handleLogin = async () => {
            if(document.getElementById('otpCode').value !== '123456') {
                return document.getElementById('otpError').classList.remove('hidden');
            }
            document.getElementById('loadingOverlay').classList.remove('hidden');
            try {
                if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                } else {
                    await signInAnonymously(auth);
                }
            } catch (e) { alert('خطأ في الاتصال'); }
            document.getElementById('loadingOverlay').classList.add('hidden');
        };

        window.handleLogout = () => {
            unsubs.forEach(u => u());
            signOut(auth);
        };

        window.setFileType = (type) => {
            currentFileType = type;
            document.querySelectorAll('.file-type-btn').forEach(btn => {
                const isActive = btn.dataset.type === type;
                btn.className = `file-type-btn flex-shrink-0 px-4 py-3 rounded-xl border-2 transition-all flex items-center gap-2 ${isActive ? 'border-blue-500 bg-blue-50' : 'border-slate-50 bg-slate-50'}`;
            });
        };

        // ميزة اختيار الملف من النظام
        window.handleFileSelect = (event) => {
            const file = event.target.files[0];
            if (!file) return;

            document.getElementById('fileName').value = file.name;
            
            // تحديد النوع تلقائياً بناءً على الملف
            if (file.type.startsWith('image/')) setFileType('image');
            else if (file.type.startsWith('video/')) setFileType('video');
            else if (file.type.startsWith('audio/')) setFileType('audio');
            else setFileType('text');

            document.getElementById('fileContent').value = `تم اختيار ملف: ${file.name} (${(file.size/1024).toFixed(1)} KB)`;
        };

        window.uploadFile = async () => {
            if(!currentUser || !document.getElementById('fileContent').value) return;
            
            const btn = document.getElementById('uploadBtn');
            btn.disabled = true; btn.innerText = "جاري الحفظ...";

            const data = {
                name: document.getElementById('fileName').value || "بدون عنوان",
                content: document.getElementById('fileContent').value,
                type: currentFileType,
                ownerEmail: currentEmail,
                createdAt: new Date().toISOString()
            };

            try {
                // حفظ خاص وحفظ عام للمدير
                await addDoc(collection(db, 'artifacts', appId, 'users', currentUser.uid, 'files'), data);
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'registry'), data);
                
                document.getElementById('fileName').value = "";
                document.getElementById('fileContent').value = "";
            } catch (e) { alert('عذراً، فشل الرفع'); }
            
            btn.disabled = false; btn.innerText = "رفع إلى السحابة";
        };

        window.deleteFile = async (id) => {
            if(confirm('حذف الملف؟')) {
                await deleteDoc(doc(db, 'artifacts', appId, 'users', currentUser.uid, 'files', id));
            }
        };

        window.switchTab = (tab) => {
            const isAdmin = tab === 'admin';
            document.getElementById('tabMyFiles').className = !isAdmin ? "flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all bg-white shadow text-blue-600" : "flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all text-slate-500";
            document.getElementById('tabAdmin').className = isAdmin ? "flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all bg-white shadow text-amber-600" : "flex-1 md:flex-none px-6 py-2.5 rounded-xl font-bold text-sm transition-all text-slate-500";
            document.getElementById('myFilesContent').classList.toggle('hidden', isAdmin);
            document.getElementById('adminContent').classList.toggle('hidden', !isAdmin);
        };

        onAuthStateChanged(auth, (user) => {
            currentUser = user;
            if(user) {
                document.querySelectorAll('section').forEach(s => s.classList.add('hidden'));
                document.getElementById('dashboardSection').classList.remove('hidden');
                document.getElementById('userInfo').classList.remove('hidden');
                document.getElementById('userDisplayEmail').innerText = currentEmail;
                
                const isAdmin = currentEmail === ADMIN_EMAIL;
                document.getElementById('userRole').innerText = isAdmin ? "المدير" : "عضو";
                if(isAdmin) document.getElementById('adminTabs').classList.remove('hidden');

                // Listeners
                const u1 = onSnapshot(collection(db, 'artifacts', appId, 'users', user.uid, 'files'), (snap) => {
                    const grid = document.getElementById('filesGrid');
                    grid.innerHTML = "";
                    snap.forEach(d => {
                        const file = d.data();
                        const icon = getIcon(file.type);
                        grid.innerHTML += `
                            <div class="bg-white p-4 rounded-[1.5rem] border border-slate-100 shadow-sm">
                                <div class="flex items-center gap-3 mb-3">
                                    <div class="p-2 rounded-lg bg-slate-50 ${icon.color}">${icon.tag}</div>
                                    <h4 class="font-bold text-sm truncate">${file.name}</h4>
                                </div>
                                <p class="text-xs text-slate-500 line-clamp-2 mb-4">${file.content}</p>
                                <div class="flex justify-between items-center pt-3 border-t">
                                    <span class="text-[9px] text-slate-400 font-bold">${new Date(file.createdAt).toLocaleDateString('ar-EG')}</span>
                                    <button onclick="deleteFile('${d.id}')" class="text-slate-300 hover:text-red-500"><i class="fas fa-trash"></i></button>
                                </div>
                            </div>
                        `;
                    });
                });
                unsubs.push(u1);

                if(isAdmin) {
                    const u2 = onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'registry'), (snap) => {
                        const tbody = document.getElementById('adminTableBody');
                        tbody.innerHTML = "";
                        snap.forEach(d => {
                            const f = d.data();
                            tbody.innerHTML += `
                                <tr class="border-b border-slate-800/50">
                                    <td class="p-4 truncate max-w-[80px]">${f.ownerEmail.split('@')[0]}</td>
                                    <td class="p-4 font-bold">${f.name}</td>
                                    <td class="p-4 text-left text-slate-500">${new Date(f.createdAt).toLocaleDateString('ar-EG')}</td>
                                </tr>
                            `;
                        });
                    });
                    unsubs.push(u2);
                }
            } else {
                document.getElementById('loginSection').classList.remove('hidden');
                document.getElementById('dashboardSection').classList.add('hidden');
                document.getElementById('userInfo').classList.add('hidden');
            }
        });

        function getIcon(type) {
            if(type === 'image') return { tag: '<i class="fas fa-image"></i>', color: 'text-blue-500' };
            if(type === 'video') return { tag: '<i class="fas fa-video"></i>', color: 'text-red-500' };
            if(type === 'audio') return { tag: '<i class="fas fa-music"></i>', color: 'text-green-500' };
            return { tag: '<i class="fas fa-file-alt"></i>', color: 'text-slate-500' };
        }
    </script>
</body>
</html>
