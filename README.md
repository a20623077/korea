<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>韓國探索網</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        .prose p { margin-bottom: 1rem; }
        .prose h2 { font-weight: 800; font-size: 1.5rem; margin-top: 1.5rem; margin-bottom: 0.5rem; }
    </style>
</head>
<body class="bg-gray-50 text-gray-900">

    <nav class="bg-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto flex items-center h-20 px-6">
            <h1 class="text-2xl font-black text-pink-600 cursor-pointer mr-10" onclick="goHome()">韓國探索網</h1>
            <button onclick="goHome()" class="mr-6 font-bold text-gray-700 hover:text-pink-600 transition">首頁</button>
            <div id="nav-menu" class="flex h-full items-center"></div>
            <button id="auth-btn" onclick="toggleLogin()" class="ml-auto text-sm bg-gray-100 px-4 py-2 rounded-full font-bold hover:bg-gray-200">
                管理員登入
            </button>
        </div>
    </nav>

    <main id="main-content" class="max-w-7xl mx-auto p-6 min-h-[60vh]">
        <!-- 內容渲染區 -->
    </main>
    <div id="login-modal" class="hidden fixed inset-0 bg-black/50 z-[60] flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-2xl w-full max-w-sm shadow-2xl">
            <h2 class="text-xl font-bold mb-6">管理員登入</h2>
            <input id="pwd-input" type="password" placeholder="請輸入密碼" onkeypress="if(event.key==='Enter') handleLogin()" class="w-full p-4 border rounded-xl mb-2" />
            <div id="login-error" class="text-red-500 text-sm mb-4 hidden">密碼錯誤，請重新輸入</div>
            <div class="flex gap-3">
                <button onclick="toggleLogin()" class="flex-1 p-3 bg-gray-100 rounded-xl">取消</button>
                <button onclick="handleLogin()" class="flex-1 p-3 bg-pink-600 text-white rounded-xl">登入</button>
            </div>
        </div>
    </div>
    <script>
        const MENU_STRUCTURE = {
            "韓國旅遊": ["首爾", "釜山", "大邱", "濟州島"],
            "韓國美食": ["首爾", "釜山", "大邱", "濟州島"],
            "韓國文化": ["傳統習俗", "韓服體驗", "節慶活動"],
            "影視娛樂": ["一代", "二代", "三代", "四代", "五代", "樂團"]
        };

        const INITIAL_POSTS = [
            { id: 1, title: "首爾景福宮：宮廷韓服體驗全攻略", category: "韓國旅遊", subCategory: "首爾", thumbnail: "https://images.unsplash.com/photo-1599577313074-123490795c65?q=80&w=800", content: "穿越時空來到首爾，穿上韓服漫步在景福宮的每一個角落。\n\n攻略建議：早上9:00入園，提前預約韓服租借。" },
            { id: 4, title: "韓國傳統習俗介紹", category: "韓國文化", subCategory: "傳統習俗", thumbnail: "https://images.unsplash.com/photo-1581452220199-6e3e57f20108?q=80&w=800", content: "韓國重視傳統節日，如春節與中秋節。推薦體驗：北村韓屋村的民俗遊戲、傳統茶道體驗。" },
            { id: 5, title: "絕美韓服體驗推薦", category: "韓國文化", subCategory: "韓服體驗", thumbnail: "https://images.unsplash.com/photo-1516035069371-29a1b244cc32?q=80&w=800", content: "體驗韓服的最佳地點莫過於景福宮周邊。\n\n推薦店家：Hanboknam (韓服男)、Gigibebe。" },
            { id: 6, title: "不可錯過的節慶活動", category: "韓國文化", subCategory: "節慶活動", thumbnail: "https://images.unsplash.com/photo-1501785888041-af3ef285b470?q=80&w=800", content: "首爾燈節與釜山煙火節是年度盛事。\n\n節慶亮點：華麗的燈光秀與絕美的煙火表演。" },
            { id: 7, title: "K-Pop 一代團：傳奇的開端", category: "影視娛樂", subCategory: "一代", thumbnail: "https://images.unsplash.com/photo-1493225255756-d99435b8004f?q=80&w=800", content: "代表團體：H.O.T., S.E.S., Shinhwa。他們奠定了韓流文化的基礎。" },
            { id: 8, title: "K-Pop 二代團：韓流全盛時期", category: "影視娛樂", subCategory: "二代", thumbnail: "https://images.unsplash.com/photo-1524368535928-3b5e04e0e5a4?q=80&w=800", content: "代表團體：BIGBANG, 少女時代, Super Junior。這是一個將韓流推向世界的黃金時期。" },
            { id: 9, title: "K-Pop 三代團：全球化的加速", category: "影視娛樂", subCategory: "三代", thumbnail: "https://images.unsplash.com/photo-1514525253161-7a46d19cd819?q=80&w=800", content: "代表團體：BTS, BLACKPINK, TWICE。數位時代的最佳代言人。" },
            { id: 10, title: "K-Pop 四代團：創新與概念的結合", category: "影視娛樂", subCategory: "四代", thumbnail: "https://images.unsplash.com/photo-1598387993441-a364f854c3e1?q=80&w=800", content: "代表團體：NewJeans, IVE, Stray Kids。強烈概念與數位影響力。" },
            { id: 11, title: "K-Pop 五代團：多元化的新勢力", category: "影視娛樂", subCategory: "五代", thumbnail: "https://images.unsplash.com/photo-1614613535308-eb5fbd3d2c17?q=80&w=800", content: "代表團體：BABYMONSTER, RIIZE, ZEROBASEONE。新時代的多元風格。" },
            { id: 12, title: "CNBLUE 與 FTISLAND：樂團的先驅", category: "影視娛樂", subCategory: "樂團", thumbnail: "https://images.unsplash.com/photo-1511671782779-c97d3d27a1d4?q=80&w=800", content: "CNBLUE 與 FTISLAND 是韓國樂團文化的指標人物，開創了偶像樂團的新篇章。" }
        ];

        let POSTS = JSON.parse(localStorage.getItem('ke_posts')) || INITIAL_POSTS;
        let state = { filter: null, isLoggedIn: !!localStorage.getItem('ke_admin'), editingId: null };

        function goHome() { state.filter = null; render('home'); }
        
        function toggleLogin() { 
            const modal = document.getElementById('login-modal');
            modal.classList.toggle('hidden'); 
            document.getElementById('login-error').classList.add('hidden');
            document.getElementById('pwd-input').value = '';
        }
        
        function handleLogin() {
            const pwd = document.getElementById('pwd-input').value;
            if(pwd === 'admin123') {
                state.isLoggedIn = true;
                localStorage.setItem('ke_admin', 'true');
                toggleLogin();
                updateAuthUI();
                render('home');
            } else { 
                document.getElementById('login-error').classList.remove('hidden');
            }
        }

        function updateAuthUI() {
            const btn = document.getElementById('auth-btn');
            if(state.isLoggedIn) {
                btn.innerText = "登出";
                btn.onclick = () => { localStorage.removeItem('ke_admin'); location.reload(); };
            }
        }

        function savePost(id) {
            const index = POSTS.findIndex(p => p.id === id);
            if(index !== -1) {
                POSTS[index].title = document.getElementById('edit-title').value;
                POSTS[index].content = document.getElementById('edit-content').value;
                
                const fileInput = document.getElementById('edit-img-file');
                if (fileInput.files && fileInput.files[0]) {
                    const reader = new FileReader();
                    reader.onload = function(e) {
                        POSTS[index].thumbnail = e.target.result;
                        localStorage.setItem('ke_posts', JSON.stringify(POSTS));
                        state.editingId = null;
                        render('read', id);
                    };
                    reader.readAsDataURL(fileInput.files[0]);
                } else {
                    localStorage.setItem('ke_posts', JSON.stringify(POSTS));
                    state.editingId = null;
                    render('read', id);
                }
            }
        }

        function render(type, postId) {
            const container = document.getElementById('main-content');
            if (type === 'read') {
                const post = POSTS.find(p => p.id === postId);
                if (state.editingId === postId) {
                    container.innerHTML = `
                        <div class="bg-white p-10 rounded-2xl shadow-sm border">
                            <label class="block font-bold mb-2">文章標題</label>
                            <input id="edit-title" value="${post.title}" class="w-full text-2xl font-black mb-6 border p-3 rounded-lg">
                            <label class="block font-bold mb-2">上傳新圖片</label>
                            <input id="edit-img-file" type="file" accept="image/*" class="w-full mb-6 border p-3 rounded-lg text-sm">
                            <label class="block font-bold mb-2">文章內容</label>
                            <textarea id="edit-content" class="w-full h-64 border p-3 rounded-lg mb-6">${post.content}</textarea>
                            <button onclick="savePost(${postId})" class="px-6 py-3 bg-pink-600 text-white rounded-xl font-bold">儲存變更</button>
                            <button onclick="state.editingId=null; render('read', ${postId})" class="px-6 py-3 bg-gray-200 text-gray-700 rounded-xl font-bold ml-2">取消</button>
                        </div>
                    `;
                } else {
                    container.innerHTML = `
                        <article class="bg-white p-10 rounded-2xl shadow-sm border">
                            <button onclick="goHome()" class="text-pink-600 font-bold mb-4">&larr; 返回列表</button>
                            ${state.isLoggedIn ? `<button onclick="state.editingId=${postId}; render('read', ${postId})" class="bg-gray-800 text-white px-4 py-2 rounded-lg mb-4 ml-4 font-bold">編輯文章</button>` : ''}
                            <h1 class="text-4xl font-black text-gray-900 mb-6">${post.title}</h1>
                            <img src="${post.thumbnail}" onerror="this.onerror=null;this.src='https://placehold.co/800x400?text=圖片無法載入';" class="w-full h-[400px] object-cover rounded-2xl mb-8">
                            <div class="prose lg:prose-xl max-w-none text-gray-700">${post.content.replace(/\n/g, '<br>')}</div>
                        </article>
                    `;
                }
            } else {
                if (!state.filter) {
                    container.innerHTML = `
                        <div class="mb-12 text-center bg-pink-50 p-12 rounded-3xl">
                            <h2 class="text-4xl font-black text-pink-600 mb-4">探索韓國文化</h2>
                            <p class="text-gray-600 text-lg">深入了解韓國迷人的文化底蘊，從傳統到現代，帶您一次看懂。</p>
                        </div>
                        <h2 class="text-2xl font-black text-gray-800 mb-8">最新精選</h2>
                        <div class="grid md:grid-cols-3 gap-6">
                            ${POSTS.map(p => `
                                <div class="cursor-pointer bg-white border rounded-xl overflow-hidden hover:shadow-xl transition" onclick="render('read', ${p.id})">
                                    <div class="h-48 overflow-hidden"><img src="${p.thumbnail}" onerror="this.onerror=null;this.src='https://placehold.co/400x300?text=無圖片';" class="w-full h-full object-cover"></div>
                                    <div class="p-5"><h3 class="font-bold text-lg">${p.title}</h3></div>
                                </div>
                            `).join('')}
                        </div>
                    `;
                } else {
                    const filtered = POSTS.filter(p => p.category === state.filter.cat && p.subCategory === state.filter.sub);
                    container.innerHTML = `
                        <div class="bg-white p-8 rounded-2xl shadow-sm border">
                            <h2 class="text-2xl font-black text-gray-800 mb-8">${state.filter.cat} - ${state.filter.sub}</h2>
                            <div class="grid md:grid-cols-3 gap-6">
                                ${filtered.map(p => `
                                    <div class="cursor-pointer border rounded-xl overflow-hidden hover:shadow-xl transition" onclick="render('read', ${p.id})">
                                        <div class="h-48 overflow-hidden"><img src="${p.thumbnail}" onerror="this.onerror=null;this.src='https://placehold.co/400x300?text=無圖片';" class="w-full h-full object-cover"></div>
                                        <div class="p-5"><h3 class="font-bold text-lg">${p.title}</h3></div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>
                    `;
                }
            }
        }
        function init() {
            const nav = document.getElementById('nav-menu');
            Object.entries(MENU_STRUCTURE).forEach(([cat, subs]) => {
                const div = document.createElement('div');
                div.className = "relative h-full flex items-center px-4 group";
                div.innerHTML = `
                    <button class="flex items-center gap-1 font-bold text-gray-700 hover:text-pink-600 transition h-full border-b-2 border-transparent hover:border-pink-600">
                        ${cat}
                    </button>
                    <div class="hidden group-hover:block absolute top-[80px] left-0 w-48 bg-white shadow-2xl rounded-b-lg border-t-2 border-pink-600 z-50">
                        ${subs.map(sub => `<button onclick="state.filter={cat:'${cat}', sub:'${sub}'}; render('home')" class="block w-full text-left px-6 py-3 text-gray-600 hover:bg-pink-50 hover:text-pink-600 transition border-b text-sm font-medium">${sub}</button>`).join('')}
                    </div>
                `;
                nav.appendChild(div);
            });
            updateAuthUI();
            render('home');
        }
        window.onload = init;
    </script>
</body>
</html>
