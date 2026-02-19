<!DOCTYPE html>
<html dir="rtl" lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Novalex - منصة التواصل</title>
    <style>
        *{margin:0;padding:0;box-sizing:border-box;font-family:system-ui,sans-serif}
        body{background:#667eea;padding:10px;padding-bottom:80px}
        
        .header{
            background:white;
            border-radius:50px;
            padding:15px;
            margin-bottom:20px;
            text-align:center;
            font-size:24px;
            font-weight:bold;
            color:#667eea;
        }
        
        .navbar{
            position:fixed;
            bottom:0;
            left:0;
            right:0;
            background:white;
            display:flex;
            justify-content:space-around;
            padding:10px;
            border-radius:30px 30px 0 0;
        }
        
        .nav-item{
            padding:5px 15px;
            cursor:pointer;
            border-radius:20px;
        }
        
        .nav-item.active{
            background:#667eea;
            color:white;
        }
        
        .post-btn{
            position:fixed;
            bottom:80px;
            right:20px;
            width:50px;
            height:50px;
            border-radius:50%;
            background:gold;
            border:none;
            font-size:24px;
            cursor:pointer;
        }
        
        .modal{
            position:fixed;
            top:0;
            left:0;
            right:0;
            bottom:0;
            background:rgba(0,0,0,0.5);
            display:none;
            justify-content:center;
            align-items:center;
        }
        
        .modal-content{
            background:white;
            border-radius:20px;
            padding:20px;
            width:90%;
            max-width:400px;
        }
        
        .post{
            background:white;
            border-radius:15px;
            padding:15px;
            margin-bottom:15px;
        }
        
        .post-header{
            display:flex;
            gap:10px;
            margin-bottom:10px;
        }
        
        .avatar{
            width:40px;
            height:40px;
            border-radius:50%;
            background:#667eea;
        }
        
        .actions{
            display:flex;
            justify-content:space-around;
            margin-top:10px;
            padding-top:10px;
            border-top:1px solid #eee;
        }
        
        .action-btn{
            cursor:pointer;
            padding:5px 15px;
        }
        
        .page{display:none}
        .page.active{display:block}
    </style>
</head>
<body>
    <div class="header">NOVALEX</div>

    <!-- الصفحة الرئيسية -->
    <div id="homePage" class="page active">
        <div id="posts"></div>
    </div>

    <!-- الملف الشخصي -->
    <div id="profilePage" class="page">
        <div style="background:white;border-radius:15px;padding:20px;text-align:center;">
            <div style="width:80px;height:80px;border-radius:50%;background:#667eea;margin:0 auto 10px;"></div>
            <h3>أحمد المصري</h3>
            <div style="display:flex;justify-content:space-around;margin:15px 0;">
                <div><strong id="postCount">1</strong><br>منشورات</div>
                <div><strong id="followCount">128</strong><br>متابعون</div>
            </div>
            <button id="followBtn" style="background:#667eea;color:white;border:none;padding:8px 25px;border-radius:20px;" onclick="toggleFollow()">متابعة</button>
        </div>
        <div id="myPosts"></div>
    </div>

    <!-- الدردشة -->
    <div id="chatPage" class="page">
        <div style="background:white;border-radius:15px;padding:15px;height:70vh;">
            <h3 style="margin-bottom:10px;">💬 الدردشة</h3>
            <div id="chatBox" style="height:50vh;overflow-y:auto;border:1px solid #eee;padding:10px;margin-bottom:10px;"></div>
            <div style="display:flex;gap:5px;">
                <input id="chatInput" style="flex:1;padding:8px;border:1px solid #ddd;border-radius:10px;" placeholder="اكتب رسالة...">
                <button onclick="sendMessage()" style="background:#667eea;color:white;border:none;padding:8px 15px;border-radius:10px;">إرسال</button>
            </div>
        </div>
    </div>

    <!-- زر النشر -->
    <button class="post-btn" onclick="openModal()">+</button>

    <!-- نافذة النشر -->
    <div class="modal" id="postModal">
        <div class="modal-content">
            <h3 style="margin-bottom:10px;">منشور جديد</h3>
            <input id="postText" style="width:100%;padding:8px;border:1px solid #ddd;border-radius:10px;margin-bottom:10px;" placeholder="اكتب منشورك...">
            <button onclick="publishPost()" style="width:100%;padding:10px;background:#667eea;color:white;border:none;border-radius:10px;">نشر</button>
        </div>
    </div>

    <!-- شريط سفلي -->
    <div class="navbar">
        <div class="nav-item active" onclick="changePage('home', this)">🏠 الرئيسية</div>
        <div class="nav-item" onclick="changePage('profile', this)">👤 حسابي</div>
        <div class="nav-item" onclick="changePage('chat', this)">💬 دردشة</div>
    </div>

    <script>
        // البيانات
        let posts = [
            {id:1, user:'أحمد', text:'مرحبا بكم في نوفالكس!', likes:5, comments:[]}
        ];
        let myPosts = [
            {id:1, user:'أحمد', text:'مرحبا بكم في نوفالكس!', likes:5, comments:[]}
        ];
        let following = false;
        let followers = 128;
        let messages = ['أحمد: مرحباً', 'سارة: كيف الحال؟'];

        // تغيير الصفحة
        function changePage(page, element) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(page + 'Page').classList.add('active');
            
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
            element.classList.add('active');
            
            if(page === 'home') showPosts();
            if(page === 'profile') showMyPosts();
            if(page === 'chat') showChat();
        }

        // عرض المنشورات
        function showPosts() {
            let html = '';
            posts.forEach(p => {
                html += `
                <div class="post">
                    <div class="post-header">
                        <div class="avatar"></div>
                        <div><strong>${p.user}</strong></div>
                    </div>
                    <p>${p.text}</p>
                    <div class="actions">
                        <span class="action-btn" onclick="likePost(${p.id})">❤️ ${p.likes}</span>
                        <span class="action-btn" onclick="commentPost(${p.id})">💬 ${p.comments.length}</span>
                    </div>
                </div>`;
            });
            document.getElementById('posts').innerHTML = html;
        }

        // عرض منشوراتي
        function showMyPosts() {
            let html = '';
            myPosts.forEach(p => {
                html += `
                <div class="post">
                    <div class="post-header">
                        <div class="avatar"></div>
                        <div><strong>${p.user}</strong></div>
                    </div>
                    <p>${p.text}</p>
                </div>`;
            });
            document.getElementById('myPosts').innerHTML = html;
            document.getElementById('postCount').innerHTML = myPosts.length;
        }

        // إعجاب
        function likePost(id) {
            let post = posts.find(p => p.id === id);
            if(post) {
                post.likes++;
                showPosts();
            }
        }

        // تعليق
        function commentPost(id) {
            let comment = prompt('اكتب تعليقك:');
            if(comment) {
                let post = posts.find(p => p.id === id);
                if(post) {
                    post.comments.push(comment);
                    showPosts();
                }
            }
        }

        // متابعة
        function toggleFollow() {
            following = !following;
            let btn = document.getElementById('followBtn');
            if(following) {
                btn.innerHTML = 'متابع ✓';
                followers++;
            } else {
                btn.innerHTML = 'متابعة';
                followers--;
            }
            document.getElementById('followCount').innerHTML = followers;
        }

        // نافذة النشر
        function openModal() {
            document.getElementById('postModal').style.display = 'flex';
        }

        // نشر
        function publishPost() {
            let text = document.getElementById('postText').value.trim();
            if(text) {
                let newPost = {
                    id: Date.now(),
                    user: 'أحمد',
                    text: text,
                    likes: 0,
                    comments: []
                };
                posts.unshift(newPost);
                myPosts.unshift({...newPost});
                
                document.getElementById('postModal').style.display = 'none';
                document.getElementById('postText').value = '';
                
                showPosts();
                showMyPosts();
                alert('✅ تم النشر');
            }
        }

        // الدردشة
        function showChat() {
            let html = '';
            messages.forEach(m => html += `<p>${m}</p>`);
            document.getElementById('chatBox').innerHTML = html;
        }

        function sendMessage() {
            let input = document.getElementById('chatInput');
            if(input.value.trim()) {
                messages.push('أنت: ' + input.value);
                showChat();
                input.value = '';
            }
        }

        // إغلاق النافذة عند الضغط خارجها
        window.onclick = function(e) {
            if(e.target.classList.contains('modal')) {
                document.getElementById('postModal').style.display = 'none';
            }
        };

        // بدء
        window.onload = function() {
            showPosts();
            showMyPosts();
            showChat();
        };
    </script>
</body>
</html>/* =============== الهيدر (معدل) =============== */
.header {
    background: var(--surface-light);
    padding: 15px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid var(--dark-200);
    z-index: 100;
    flex-shrink: 0;
    height: 70px;
    width: 100%;
    box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    transition: all var(--transition-normal);
}

body.dark-mode .header {
    background: var(--dark-800);
    border-color: var(--dark-700);
    box-shadow: 0 4px 10px rgba(0,0,0,0.2);
}

.logo {
    font-size: 26px;
    font-weight: 800;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600), var(--accent-600));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    cursor: pointer;
    transition: all var(--transition-fast);
    letter-spacing: -0.5px;
}

.logo:hover {
    transform: scale(1.05) rotate(-1deg);
}

.header-actions {
    display: flex;
    gap: 8px;
    align-items: center;
}

.header-btn {
    background: var(--dark-50);
    border: none;
    font-size: 20px;
    cursor: pointer;
    color: var(--dark-500);
    transition: all var(--transition-fast);
    width: 40px;
    height: 40px;
    border-radius: var(--radius-full);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
}

body.dark-mode .header-btn {
    background: var(--dark-700);
    color: var(--dark-300);
}

.header-btn:hover {
    background: var(--primary-100);
    color: var(--primary-600);
    transform: rotate(5deg) scale(1.1);
}

body.dark-mode .header-btn:hover {
    background: var(--primary-900);
    color: var(--primary-400);
}

.badge {
    position: relative;
}

.badge::after {
    content: attr(data-count);
    position: absolute;
    top: -3px;
    right: -3px;
    background: var(--danger-500);
    color: white;
    font-size: 10px;
    font-weight: 700;
    width: 18px;
    height: 18px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    animation: pulse 2s infinite;
    border: 2px solid var(--surface-light);
}

body.dark-mode .badge::after {
    border-color: var(--dark-800);
}

/* =============== المحتوى الرئيسي (معدل) =============== */
.main-content {
    flex: 1 1 auto;
    overflow-y: auto;
    overflow-x: hidden;
    scroll-behavior: smooth;
    width: 100%;
    position: relative;
    -webkit-overflow-scrolling: touch;
}

.main-content::-webkit-scrollbar {
    width: 4px;
}

.main-content::-webkit-scrollbar-track {
    background: var(--dark-200);
    border-radius: var(--radius-full);
}

.main-content::-webkit-scrollbar-thumb {
    background: var(--primary-500);
    border-radius: var(--radius-full);
}

body.dark-mode .main-content::-webkit-scrollbar-track {
    background: var(--dark-700);
}

.page {
    display: none;
    width: 100%;
    min-height: 100%;
    animation: pageFade 0.3s ease;
}

@keyframes pageFade {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

.page.active-page {
    display: block;
}

/* =============== الفيد العمودي للمنشورات =============== */
.main-feed {
    display: flex;
    flex-direction: column;
    gap: 15px;
    padding: 10px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
}

.feed-item {
    background: var(--surface-light);
    border-radius: var(--radius-xl);
    overflow: hidden;
    box-shadow: var(--shadow-md);
    border: 1px solid var(--dark-200);
    transition: all var(--transition-normal);
    width: 100%;
}

body.dark-mode .feed-item {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.feed-item:hover {
    transform: translateY(-3px);
    box-shadow: var(--shadow-lg);
}

.feed-header {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 15px;
    border-bottom: 1px solid var(--dark-200);
}

body.dark-mode .feed-header {
    border-color: var(--dark-700);
}

.feed-avatar {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 18px;
    cursor: pointer;
    flex-shrink: 0;
    box-shadow: 0 4px 8px var(--primary-200);
}

body.dark-mode .feed-avatar {
    box-shadow: 0 4px 8px rgba(0,0,0,0.3);
}

.feed-user-info {
    flex: 1;
}

.feed-username {
    font-weight: 700;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    gap: 6px;
    cursor: pointer;
    font-size: 16px;
}

body.dark-mode .feed-username {
    color: var(--dark-100);
}

.feed-time {
    font-size: 12px;
    color: var(--dark-500);
    display: flex;
    align-items: center;
    gap: 5px;
}

.feed-content-text {
    padding: 15px;
    font-size: 15px;
    line-height: 1.6;
    color: var(--dark-800);
    word-break: break-word;
}

body.dark-mode .feed-content-text {
    color: var(--dark-200);
}

.feed-media {
    width: 100%;
    max-height: 500px;
    overflow: hidden;
    background: #000;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
}

.feed-media img,
.feed-media video {
    width: 100%;
    height: auto;
    max-height: 500px;
    object-fit: contain;
    background: #000;
    cursor: pointer;
}

.feed-actions {
    display: flex;
    padding: 12px 15px;
    border-top: 1px solid var(--dark-200);
    gap: 15px;
    flex-wrap: wrap;
}

body.dark-mode .feed-actions {
    border-color: var(--dark-700);
}

.feed-action {
    background: none;
    border: none;
    color: var(--dark-600);
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all var(--transition-fast);
    padding: 5px 10px;
    border-radius: var(--radius-full);
}

.feed-action:hover {
    background: var(--dark-100);
    color: var(--primary-600);
}

body.dark-mode .feed-action:hover {
    background: var(--dark-700);
}

.feed-action i {
    font-size: 20px;
}

.feed-action.liked i {
    color: #ff6b6b;
    animation: heartPop 0.3s ease;
}

@keyframes heartPop {
    0% { transform: scale(1); }
    50% { transform: scale(1.3); }
    100% { transform: scale(1); }
}

/* زر الترويج في المنشور */
.feed-promo-btn {
    background: linear-gradient(135deg, #ffd700, #ffb347);
    color: #000;
    border: none;
    border-radius: var(--radius-full);
    padding: 5px 15px;
    font-weight: 700;
    font-size: 13px;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    gap: 5px;
    margin-right: auto;
}
/* =============== الهيدر (معدل) =============== */
.header {
    background: var(--surface-light);
    padding: 15px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid var(--dark-200);
    z-index: 100;
    flex-shrink: 0;
    height: 70px;
    width: 100%;
    box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    transition: all var(--transition-normal);
}

body.dark-mode .header {
    background: var(--dark-800);
    border-color: var(--dark-700);
    box-shadow: 0 4px 10px rgba(0,0,0,0.2);
}

.logo {
    font-size: 26px;
    font-weight: 800;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600), var(--accent-600));
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    cursor: pointer;
    transition: all var(--transition-fast);
    letter-spacing: -0.5px;
}

.logo:hover {
    transform: scale(1.05) rotate(-1deg);
}

.header-actions {
    display: flex;
    gap: 8px;
    align-items: center;
}

.header-btn {
    background: var(--dark-50);
    border: none;
    font-size: 20px;
    cursor: pointer;
    color: var(--dark-500);
    transition: all var(--transition-fast);
    width: 40px;
    height: 40px;
    border-radius: var(--radius-full);
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
}

body.dark-mode .header-btn {
    background: var(--dark-700);
    color: var(--dark-300);
}

.header-btn:hover {
    background: var(--primary-100);
    color: var(--primary-600);
    transform: rotate(5deg) scale(1.1);
}

body.dark-mode .header-btn:hover {
    background: var(--primary-900);
    color: var(--primary-400);
}

.badge {
    position: relative;
}

.badge::after {
    content: attr(data-count);
    position: absolute;
    top: -3px;
    right: -3px;
    background: var(--danger-500);
    color: white;
    font-size: 10px;
    font-weight: 700;
    width: 18px;
    height: 18px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    animation: pulse 2s infinite;
    border: 2px solid var(--surface-light);
}

body.dark-mode .badge::after {
    border-color: var(--dark-800);
}

/* =============== المحتوى الرئيسي (معدل) =============== */
.main-content {
    flex: 1 1 auto;
    overflow-y: auto;
    overflow-x: hidden;
    scroll-behavior: smooth;
    width: 100%;
    position: relative;
    -webkit-overflow-scrolling: touch;
}

.main-content::-webkit-scrollbar {
    width: 4px;
}

.main-content::-webkit-scrollbar-track {
    background: var(--dark-200);
    border-radius: var(--radius-full);
}

.main-content::-webkit-scrollbar-thumb {
    background: var(--primary-500);
    border-radius: var(--radius-full);
}

body.dark-mode .main-content::-webkit-scrollbar-track {
    background: var(--dark-700);
}

.page {
    display: none;
    width: 100%;
    min-height: 100%;
    animation: pageFade 0.3s ease;
}

@keyframes pageFade {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
}

.page.active-page {
    display: block;
}

/* =============== الفيد العمودي للمنشورات =============== */
.main-feed {
    display: flex;
    flex-direction: column;
    gap: 15px;
    padding: 10px;
    width: 100%;
    max-width: 100%;
    box-sizing: border-box;
}

.feed-item {
    background: var(--surface-light);
    border-radius: var(--radius-xl);
    overflow: hidden;
    box-shadow: var(--shadow-md);
    border: 1px solid var(--dark-200);
    transition: all var(--transition-normal);
    width: 100%;
}

body.dark-mode .feed-item {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.feed-item:hover {
    transform: translateY(-3px);
    box-shadow: var(--shadow-lg);
}

.feed-header {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 15px;
    border-bottom: 1px solid var(--dark-200);
}

body.dark-mode .feed-header {
    border-color: var(--dark-700);
}

.feed-avatar {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 18px;
    cursor: pointer;
    flex-shrink: 0;
    box-shadow: 0 4px 8px var(--primary-200);
}

body.dark-mode .feed-avatar {
    box-shadow: 0 4px 8px rgba(0,0,0,0.3);
}

.feed-user-info {
    flex: 1;
}

.feed-username {
    font-weight: 700;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    gap: 6px;
    cursor: pointer;
    font-size: 16px;
}

body.dark-mode .feed-username {
    color: var(--dark-100);
}

.feed-time {
    font-size: 12px;
    color: var(--dark-500);
    display: flex;
    align-items: center;
    gap: 5px;
}

.feed-content-text {
    padding: 15px;
    font-size: 15px;
    line-height: 1.6;
    color: var(--dark-800);
    word-break: break-word;
}

body.dark-mode .feed-content-text {
    color: var(--dark-200);
}

.feed-media {
    width: 100%;
    max-height: 500px;
    overflow: hidden;
    background: #000;
    position: relative;
    display: flex;
    justify-content: center;
    align-items: center;
}

.feed-media img,
.feed-media video {
    width: 100%;
    height: auto;
    max-height: 500px;
    object-fit: contain;
    background: #000;
    cursor: pointer;
}

.feed-actions {
    display: flex;
    padding: 12px 15px;
    border-top: 1px solid var(--dark-200);
    gap: 15px;
    flex-wrap: wrap;
}

body.dark-mode .feed-actions {
    border-color: var(--dark-700);
}

.feed-action {
    background: none;
    border: none;
    color: var(--dark-600);
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all var(--transition-fast);
    padding: 5px 10px;
    border-radius: var(--radius-full);
}

.feed-action:hover {
    background: var(--dark-100);
    color: var(--primary-600);
}

body.dark-mode .feed-action:hover {
    background: var(--dark-700);
}

.feed-action i {
    font-size: 20px;
}

.feed-action.liked i {
    color: #ff6b6b;
    animation: heartPop 0.3s ease;
}

@keyframes heartPop {
    0% { transform: scale(1); }
    50% { transform: scale(1.3); }
    100% { transform: scale(1); }
}

/* زر الترويج في المنشور */
.feed-promo-btn {
    background: linear-gradient(135deg, #ffd700, #ffb347);
    color: #000;
    border: none;
    border-radius: var(--radius-full);
    padding: 5px 15px;
    font-weight: 700;
    font-size: 13px;
    cursor: pointer;
    display: inline-flex;
    align-items: center;
    gap: 5px;
    margin-right: auto;
}/* =============== القصص (معدلة للعرض الأفقي والدوائر) =============== */
.stories-section {
    background: var(--surface-light);
    padding: 15px 0;
    overflow-x: auto;
    overflow-y: hidden;
    white-space: nowrap;
    margin-bottom: 10px;
    width: 100%;
    border-bottom: 1px solid var(--dark-200);
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
}

.stories-section::-webkit-scrollbar {
    display: none;
}

.stories-container {
    display: inline-flex;
    gap: 18px;
    padding: 0 20px;
    flex-direction: row;
}

.story-item {
    display: inline-flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
    width: 75px;
    cursor: pointer;
    transition: all var(--transition-fast);
    text-align: center;
}

.story-item:hover {
    transform: translateY(-3px);
}

.story-ring {
    width: 70px;
    height: 70px;
    border-radius: 50%;
    background: linear-gradient(45deg, var(--primary-600), var(--secondary-600), var(--accent-600));
    padding: 3px;
    display: flex;
    align-items: center;
    justify-content: center;
    animation: rotate 10s linear infinite;
}

.story-avatar {
    width: 100%;
    height: 100%;
    border-radius: 50%;
    border: 3px solid var(--surface-light);
    background: var(--dark-200);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 30px;
    font-weight: 600;
    color: var(--dark-700);
    transition: all var(--transition-fast);
    object-fit: cover;
}

body.dark-mode .story-avatar {
    border-color: var(--dark-800);
    background: var(--dark-700);
    color: var(--dark-300);
}

.story-add {
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white !important;
}

.story-name {
    font-size: 12px;
    font-weight: 600;
    max-width: 70px;
    overflow: hidden;
    text-overflow: ellipsis;
    color: var(--dark-700);
    white-space: nowrap;
}

body.dark-mode .story-name {
    color: var(--dark-300);
}

/* =============== إنشاء منشور (على الصفحة الرئيسية) =============== */
.create-post {
    background: var(--surface-light);
    border-radius: var(--radius-xl);
    padding: 18px;
    margin: 15px;
    box-shadow: var(--shadow-md);
    border: 1px solid var(--dark-200);
    transition: all var(--transition-normal);
}

body.dark-mode .create-post {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.create-post-header {
    display: flex;
    gap: 12px;
    margin-bottom: 15px;
}

.user-avatar {
    width: 48px;
    height: 48px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 20px;
    flex-shrink: 0;
    box-shadow: 0 4px 8px var(--primary-200);
}

body.dark-mode .user-avatar {
    box-shadow: 0 4px 8px rgba(0,0,0,0.3);
}

.post-input {
    flex: 1;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-xl);
    padding: 14px 18px;
    outline: none;
    resize: none;
    font-size: 15px;
    background: var(--surface-light);
    color: var(--dark-900);
    min-height: 60px;
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Cairo', sans-serif;
    transition: all var(--transition-fast);
}

body.dark-mode .post-input {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-100);
}

.post-input:focus {
    border-color: var(--primary-500);
    box-shadow: 0 0 0 4px var(--primary-100);
    transform: translateY(-2px);
}

.post-char-counter {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin: 10px 0;
    padding: 8px 15px;
    background: var(--dark-100);
    border-radius: var(--radius-full);
    border: 1px solid var(--dark-200);
}

body.dark-mode .post-char-counter {
    background: var(--dark-700);
    border-color: var(--dark-600);
}

.post-char-text {
    font-size: 13px;
    font-weight: 600;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    gap: 6px;
}

.post-char-text i {
    color: var(--primary-500);
}

.post-char-progress {
    width: 100px;
    height: 6px;
    background: var(--dark-300);
    border-radius: var(--radius-full);
    overflow: hidden;
}

body.dark-mode .post-char-progress {
    background: var(--dark-600);
}

.post-char-bar {
    height: 100%;
    background: linear-gradient(90deg, var(--primary-500), var(--secondary-500));
    width: 0%;
    transition: width 0.2s ease;
    border-radius: var(--radius-full);
}

.post-char-bar.warning {
    background: linear-gradient(90deg, var(--warning-500), var(--warning-400));
}

.post-char-bar.danger {
    background: linear-gradient(90deg, var(--danger-500), var(--danger-400));
}

.post-media-actions {
    display: flex;
    gap: 10px;
    margin-top: 10px;
}

.post-media-btn {
    flex: 1;
    padding: 10px;
    background: var(--dark-100);
    border: 1px solid var(--dark-200);
    border-radius: var(--radius-full);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    cursor: pointer;
    font-size: 13px;
    font-weight: 600;
    color: var(--dark-700);
    transition: all var(--transition-fast);
}

body.dark-mode .post-media-btn {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-300);
}

.post-media-btn:hover {
    background: var(--primary-50);
    border-color: var(--primary-500);
    color: var(--primary-700);
    transform: translateY(-2px);
}

body.dark-mode .post-media-btn:hover {
    background: var(--dark-600);
    border-color: var(--primary-500);
    color: var(--primary-400);
}

.post-media-btn i {
    font-size: 16px;
    color: var(--primary-500);
}

.post-media-btn:nth-child(2) i {
    color: var(--accent-500);
}

.post-media-btn:nth-child(3) i {
    color: var(--success-500);
}

/* =============== الإعلانات =============== */
.ad-item {
    position: relative;
    background: linear-gradient(135deg, #1e1e2f, #2d2d44);
}

.ad-badge {
    position: absolute;
    top: 20px;
    left: 20px;
    background: rgba(255,193,7,0.95);
    color: #000;
    padding: 6px 12px;
    border-radius: var(--radius-full);
    font-size: 12px;
    font-weight: 600;
    display: flex;
    align-items: center;
    gap: 6px;
    z-index: 20;
    box-shadow: 0 4px 10px rgba(0,0,0,0.3);
    backdrop-filter: blur(5px);
    border: 1px solid white;
    animation: adPulse 2s infinite;
}

@keyframes adPulse {
    0%,100% { transform: scale(1); }
    50% { transform: scale(1.05); }
}

.ad-brand {
    display: flex;
    align-items: center;
    gap: 8px;
    color: white;
    font-weight: 700;
    margin-bottom: 10px;
    font-size: 18px;
    text-shadow: 0 2px 4px rgba(0,0,0,0.3);
}

.ad-brand i {
    color: #ffd700;
}

.ad-progress {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 4px;
    background: rgba(255,255,255,0.2);
    z-index: 25;
}

.ad-progress-bar {
    height: 100%;
    background: linear-gradient(90deg, #ffd700, #ffb347);
    width: 0%;
    transition: width 1s linear;
}

.ad-cta {
    background: linear-gradient(135deg, #ffd700, #ffb347);
    color: #000;
    border: none;
    border-radius: var(--radius-full);
    padding: 8px 20px;
    font-weight: 700;
    cursor: pointer;
    margin-top: 10px;
    display: inline-block;
}

/* =============== نافذة التعليقات المتكاملة =============== */
.comments-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 8000;
    display: none;
    align-items: flex-end;
}

.comments-modal.active {
    display: flex;
}

.comments-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0,0,0,0.6);
    backdrop-filter: blur(8px);
}

.comments-content {
    position: relative;
    width: 100%;
    max-height: 80vh;
    background: var(--surface-light);
    border-radius: var(--radius-2xl) var(--radius-2xl) 0 0;
    padding: 25px 20px;
    animation: slideUp 0.4s ease;
    overflow: hidden;
    display: flex;
    flex-direction: column;
    border-top: 3px solid var(--primary-500);
}

body.dark-mode .comments-content {
    background: var(--dark-800);
}

.comments-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
    padding-bottom: 15px;
    border-bottom: 2px solid var(--dark-200);
}

body.dark-mode .comments-header {
    border-color: var(--dark-700);
}

.comments-header h3 {
    font-size: 20px;
    font-weight: 700;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    gap: 8px;
}

body.dark-mode .comments-header h3 {
    color: var(--dark-100);
}

.comments-header h3 i {
    color: var(--primary-500);
}

.comments-close {
    background: var(--dark-200);
    border: none;
    width: 42px;
    height: 42px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 20px;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all var(--transition-fast);
}

body.dark-mode .comments-close {
    background: var(--dark-700);
    color: var(--dark-300);
}

.comments-close:hover {
    background: var(--danger-500);
    color: white;
    transform: rotate(90deg);
}

.comments-list {
    flex: 1;
    overflow-y: auto;
    padding: 10px 0;
    max-height: 50vh;
}

.comment-item {
    margin-bottom: 20px;
    border-bottom: 1px solid var(--dark-200);
    padding-bottom: 15px;
}

body.dark-mode .comment-item {
    border-color: var(--dark-700);
}

.comment-main {
    display: flex;
    gap: 12px;
}

.comment-avatar {
    width: 45px;
    height: 45px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 16px;
    flex-shrink: 0;
    border: 2px solid var(--surface-light);
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

body.dark-mode .comment-avatar {
    border-color: var(--dark-800);
}

.comment-content {
    flex: 1;
}

.comment-header {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 6px;
    flex-wrap: wrap;
}

.comment-username {
    font-weight: 700;
    font-size: 15px;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    gap: 5px;
}

body.dark-mode .comment-username {
    color: var(--dark-100);
}

.comment-username i {
    color: var(--primary-500);
}

.comment-time {
    font-size: 11px;
    color: var(--dark-500);
    background: var(--dark-100);
    padding: 3px 8px;
    border-radius: var(--radius-full);
}

body.dark-mode .comment-time {
    background: var(--dark-700);
    color: var(--dark-400);
}

.comment-text {
    font-size: 15px;
    color: var(--dark-800);
    line-height: 1.6;
    margin-bottom: 8px;
}

body.dark-mode .comment-text {
    color: var(--dark-200);
}

.comment-actions {
    display: flex;
    gap: 20px;
    margin-bottom: 10px;
}

.comment-action {
    background: none;
    border: none;
    color: var(--dark-600);
    font-size: 13px;
    font-weight: 600;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 5px 12px;
    border-radius: var(--radius-full);
    transition: all var(--transition-fast);
}

.comment-action:hover {
    background: var(--dark-100);
    color: var(--primary-600);
}

body.dark-mode .comment-action:hover {
    background: var(--dark-700);
}

.comment-action i {
    font-size: 16px;
}

.comment-action.liked i {
    color: #ff6b6b;
}

.comment-input-area {
    display: flex;
    gap: 12px;
    padding: 20px 0 10px;
    border-top: 2px solid var(--dark-200);
    margin-top: 15px;
    background: var(--surface-light);
}

body.dark-mode .comment-input-area {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.comment-input {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-full);
    outline: none;
    background: var(--dark-100);
    color: var(--dark-900);
    font-size: 15px;
    transition: all var(--transition-fast);
}

body.dark-mode .comment-input {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-100);
}

.comment-input:focus {
    border-color: var(--primary-500);
    box-shadow: 0 0 0 4px var(--primary-100);
    transform: translateY(-2px);
}

.comment-send {
    width: 55px;
    height: 55px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    border: none;
    cursor: pointer;
    font-size: 20px;
    transition: all var(--transition-fast);
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 6px 12px rgba(99,102,241,0.3);
}

.comment-send:hover {
    transform: scale(1.1) rotate(5deg);
    box-shadow: 0 10px 20px rgba(99,102,241,0.5);
}

/* =============== العدادات المتطورة =============== */
.advanced-stats-section {
    background: linear-gradient(135deg, var(--surface-light), var(--dark-50));
    border-radius: var(--radius-2xl);
    padding: 20px;
    margin: 20px 15px;
    border: 1px solid var(--dark-200);
    box-shadow: 0 10px 30px rgba(0,0,0,0.05);
    transition: all var(--transition-normal);
}

body.dark-mode .advanced-stats-section {
    background: linear-gradient(135deg, var(--dark-800), var(--dark-900));
    border-color: var(--dark-700);
}

.advanced-stats-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
    padding-bottom: 15px;
    border-bottom: 2px solid var(--primary-200);
    cursor: pointer;
    transition: all var(--transition-fast);
}

body.dark-mode .advanced-stats-header {
    border-color: var(--primary-800);
}

.advanced-stats-header:hover {
    opacity: 0.9;
}

.advanced-stats-header h3 {
    font-size: 20px;
    font-weight: 800;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    gap: 12px;
    letter-spacing: -0.5px;
}

body.dark-mode .advanced-stats-header h3 {
    color: var(--dark-100);
}

.advanced-stats-header h3 i {
    color: var(--primary-500);
    font-size: 24px;
    background: var(--primary-100);
    padding: 8px;
    border-radius: var(--radius-lg);
}

.advanced-stats-header .toggle-icon {
    color: var(--primary-500);
    font-size: 22px;
    transition: transform var(--transition-normal);
    background: var(--primary-50);
    width: 40px;
    height: 40px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.advanced-stats-header.active .toggle-icon {
    transform: rotate(180deg);
}

.advanced-stats-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
    max-height: 0;
    overflow: hidden;
    transition: max-height 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.advanced-stats-header.active + .advanced-stats-grid {
    max-height: 800px;
}

@media (min-width: 480px) {
    .advanced-stats-grid {
        grid-template-columns: repeat(4, 1fr);
    }
}

.advanced-stat-card {
    background: var(--surface-light);
    border-radius: var(--radius-xl);
    padding: 20px 15px;
    display: flex;
    align-items: center;
    gap: 15px;
    transition: all var(--transition-normal);
    cursor: pointer;
    border: 1px solid var(--dark-200);
    box-shadow: 0 4px 12px rgba(0,0,0,0.02);
    position: relative;
    overflow: hidden;
}

body.dark-mode .advanced-stat-card {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.advanced-stat-card::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 4px;
    height: 100%;
    background: linear-gradient(135deg, var(--primary-500), var(--secondary-500));
    opacity: 0;
    transition: opacity var(--transition-fast);
}

.advanced-stat-card:hover::before {
    opacity: 1;
}

.advanced-stat-card:hover {
    transform: translateY(-4px) scale(1.02);
    box-shadow: var(--shadow-xl);
    border-color: transparent;
}

.advanced-stat-icon {
    width: 55px;
    height: 55px;
    border-radius: 20px;
    background: linear-gradient(135deg, var(--primary-100), var(--secondary-100));
    color: var(--primary-600);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 26px;
    transition: all var(--transition-fast);
    box-shadow: 0 8px 16px rgba(79,70,229,0.15);
}

body.dark-mode .advanced-stat-icon {
    background: linear-gradient(135deg, var(--primary-900), var(--secondary-900));
    color: var(--primary-400);
}

.advanced-stat-card:hover .advanced-stat-icon {
    transform: scale(1.1) rotate(5deg);
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
}

.advanced-stat-content {
    flex: 1;
}

.advanced-stat-value {
    font-size: 24px;
    font-weight: 800;
    color: var(--dark-900);
    margin-bottom: 6px;
    line-height: 1.2;
    display: flex;
    align-items: center;
    gap: 5px;
}

body.dark-mode .advanced-stat-value {
    color: var(--dark-100);
}

.advanced-stat-label {
    font-size: 13px;
    color: var(--dark-600);
    display: flex;
    align-items: center;
    gap: 6px;
    font-weight: 500;
}

body.dark-mode .advanced-stat-label {
    color: var(--dark-400);
}

.advanced-stat-label i {
    font-size: 12px;
    color: var(--primary-500);
}

.cs-stat-card {
    background: linear-gradient(135deg, #fff5f0, #ffe9e0);
    border: 2px solid var(--primary-400);
}

body.dark-mode .cs-stat-card {
    background: linear-gradient(135deg, #2a1f1a, #1f1510);
}

.cs-stat-card .advanced-stat-icon {
    background: var(--primary-600);
    color: white;
}

.ai-stat-card {
    background: linear-gradient(135deg, #e6f7f0, #d0f0e6);
    border: 2px solid #10a37f;
}

body.dark-mode .ai-stat-card {
    background: linear-gradient(135deg, #0a2e24, #0a231c);
}

.ai-stat-card .advanced-stat-icon {
    background: #10a37f;
    color: white;
}/* =============== البث المباشر =============== */
.live-page {
    background: var(--dark-900);
    min-height: 100%;
    color: white;
}

.live-header {
    padding: 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
    background: linear-gradient(135deg, var(--primary-700), var(--secondary-700));
    position: relative;
    overflow: hidden;
}

.live-header::before {
    content: '';
    position: absolute;
    top: -50%;
    left: -50%;
    width: 200%;
    height: 200%;
    background: repeating-linear-gradient(45deg,transparent,transparent 10px,rgba(255,255,255,0.1) 10px,rgba(255,255,255,0.1) 20px);
    animation: moveBackground 20s linear infinite;
}

@keyframes moveBackground {
    from { transform: translate(0,0); }
    to { transform: translate(50px,50px); }
}

.live-header h2 {
    font-size: 22px;
    font-weight: 700;
    position: relative;
    z-index: 1;
    display: flex;
    align-items: center;
    gap: 10px;
}

.live-header h2 i {
    animation: pulse 2s infinite;
    color: #ff6b6b;
}

.start-live-btn {
    padding: 12px 24px;
    background: rgba(255,255,255,0.2);
    border: 2px solid rgba(255,255,255,0.3);
    border-radius: var(--radius-full);
    color: white;
    font-size: 15px;
    font-weight: 700;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 10px;
    transition: all var(--transition-normal);
    position: relative;
    z-index: 1;
    backdrop-filter: blur(10px);
}

.start-live-btn:hover {
    background: rgba(255,255,255,0.3);
    transform: translateY(-3px);
    box-shadow: 0 10px 25px rgba(0,0,0,0.3);
}

.live-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
    padding: 20px;
}

@media (min-width: 768px) {
    .live-grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

@media (min-width: 1024px) {
    .live-grid {
        grid-template-columns: repeat(4, 1fr);
    }
}

.live-card {
    background: var(--dark-800);
    border-radius: var(--radius-xl);
    overflow: hidden;
    position: relative;
    aspect-ratio: 9/16;
    cursor: pointer;
    transition: all var(--transition-normal);
    border: 2px solid transparent;
}

.live-card:hover {
    transform: translateY(-5px) scale(1.02);
    box-shadow: 0 20px 30px rgba(0,0,0,0.5);
    border-color: var(--primary-500);
}

.live-card img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    transition: transform 0.4s;
}

.live-card:hover img {
    transform: scale(1.05);
}

.live-badge {
    position: absolute;
    top: 12px;
    right: 12px;
    background: linear-gradient(135deg, var(--danger-600), var(--danger-500));
    color: white;
    padding: 6px 12px;
    border-radius: var(--radius-full);
    font-size: 12px;
    font-weight: 700;
    display: flex;
    align-items: center;
    gap: 6px;
    animation: pulse 2s infinite;
    z-index: 2;
    box-shadow: 0 4px 10px rgba(220,38,38,0.3);
}

.live-viewer-count {
    position: absolute;
    bottom: 12px;
    left: 12px;
    background: rgba(0,0,0,0.7);
    color: white;
    padding: 6px 12px;
    border-radius: var(--radius-full);
    font-size: 12px;
    display: flex;
    align-items: center;
    gap: 6px;
    backdrop-filter: blur(5px);
    border: 1px solid rgba(255,255,255,0.2);
    z-index: 2;
}

.live-user {
    position: absolute;
    bottom: 12px;
    right: 12px;
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(0,0,0,0.6);
    padding: 6px 12px;
    border-radius: var(--radius-full);
    backdrop-filter: blur(5px);
    border: 1px solid rgba(255,255,255,0.2);
    z-index: 2;
}

.live-user-avatar {
    width: 32px;
    height: 32px;
    border-radius: 50%;
    border: 2px solid var(--primary-600);
    background: var(--dark-700);
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-weight: bold;
    font-size: 14px;
}

.live-stream-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: #000;
    z-index: 8000;
    display: none;
    flex-direction: column;
}

.live-stream-modal.active {
    display: flex;
}

.live-stream-video {
    flex: 1;
    width: 100%;
    background: #000;
    position: relative;
}

.live-stream-video video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.live-stream-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    padding: 20px;
    background: linear-gradient(to bottom, rgba(0,0,0,0.8), transparent);
    display: flex;
    justify-content: space-between;
    align-items: center;
    z-index: 10;
}

.live-stream-info {
    display: flex;
    align-items: center;
    gap: 15px;
}

.live-stream-avatar {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    border: 3px solid white;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    display: flex;
    align-items: center;
    justify-content: center;
    color: white;
    font-weight: bold;
    font-size: 20px;
}

.live-stream-details h3 {
    font-size: 18px;
    font-weight: 700;
    margin-bottom: 5px;
}

.live-stream-details p {
    font-size: 13px;
    opacity: 0.8;
    display: flex;
    align-items: center;
    gap: 5px;
}

.live-stream-details i {
    color: #ff6b6b;
    font-size: 10px;
}

.live-stream-close {
    background: rgba(0,0,0,0.5);
    border: 1px solid rgba(255,255,255,0.3);
    color: white;
    width: 45px;
    height: 45px;
    border-radius: 50%;
    font-size: 20px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all var(--transition-fast);
    backdrop-filter: blur(5px);
}

.live-stream-close:hover {
    background: rgba(255,255,255,0.2);
    transform: rotate(90deg);
}

.gifts-toggle-btn {
    position: absolute;
    bottom: 90px;
    right: 20px;
    background: gold;
    border: none;
    color: #000;
    padding: 12px 24px;
    border-radius: var(--radius-full);
    font-weight: 700;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 8px;
    z-index: 20;
    backdrop-filter: blur(5px);
    border: 2px solid white;
    animation: pulse 2s infinite;
}

.gifts-toggle-btn:hover {
    transform: scale(1.05);
    background: #ffd700;
}

.live-gifts-section {
    position: absolute;
    bottom: 150px;
    left: 20px;
    right: 20px;
    background: rgba(30,30,30,0.95);
    border-radius: var(--radius-2xl);
    padding: 20px;
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255,255,255,0.2);
    display: none;
    z-index: 25;
}

.live-gifts-section.active {
    display: block;
}

.gifts-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
    color: white;
}

.gifts-header h4 {
    font-size: 18px;
    display: flex;
    align-items: center;
    gap: 8px;
}

.gifts-header h4 i {
    color: #ffd700;
}

.gifts-close {
    background: none;
    border: none;
    color: white;
    font-size: 20px;
    cursor: pointer;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
}

.gifts-close:hover {
    background: rgba(255,255,255,0.2);
}

.gifts-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
    margin: 15px 0;
}

.gift-item {
    background: rgba(255,255,255,0.1);
    border-radius: var(--radius-lg);
    padding: 12px 5px;
    text-align: center;
    cursor: pointer;
    transition: all var(--transition-fast);
    border: 2px solid transparent;
}

.gift-item:hover {
    background: rgba(255,255,255,0.2);
    transform: translateY(-3px);
    border-color: gold;
}

.gift-emoji {
    font-size: 28px;
    margin-bottom: 8px;
    display: block;
}

.gift-name {
    font-size: 11px;
    color: white;
    margin-bottom: 4px;
    font-weight: 600;
}

.gift-price {
    font-size: 10px;
    color: gold;
}

.send-gift-btn {
    width: 100%;
    padding: 14px;
    background: linear-gradient(135deg, #ffd700, #ffb347);
    border: none;
    border-radius: var(--radius-full);
    color: #000;
    font-weight: 700;
    font-size: 16px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    transition: all var(--transition-fast);
}

.send-gift-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 10px 20px rgba(255,215,0,0.4);
}

/* =============== البحث =============== */
.search-page {
    padding: 15px;
}

.search-bar {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-input {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-full);
    outline: none;
    font-size: 15px;
    background: var(--surface-light);
}

.search-input:focus {
    border-color: var(--primary-500);
    box-shadow: 0 0 0 3px var(--primary-100);
}

.search-btn {
    width: 55px;
    height: 55px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    border: none;
    cursor: pointer;
    font-size: 20px;
}

.search-filters {
    display: flex;
    gap: 10px;
    overflow-x: auto;
    padding-bottom: 5px;
}

.filter-chip {
    padding: 10px 20px;
    background: var(--dark-100);
    border-radius: var(--radius-full);
    white-space: nowrap;
    cursor: pointer;
    border: 1px solid var(--dark-200);
}

.filter-chip.active {
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
}

.search-results {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2px;
    margin-top: 15px;
}

@media (max-width: 480px) {
    .search-results {
        grid-template-columns: repeat(2, 1fr);
    }
}

/* =============== الدردشة =============== */
.chat-page {
    padding: 15px;
}

.chat-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    background: var(--surface-light);
    border-radius: var(--radius-lg);
    cursor: pointer;
    border: 1px solid var(--dark-200);
}

.chat-item:hover {
    transform: translateX(-5px);
    box-shadow: var(--shadow-md);
}

.chat-avatar {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
    position: relative;
}

.online-status {
    position: absolute;
    bottom: 2px;
    right: 2px;
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: var(--success-500);
    border: 2px solid white;
}

.chat-info {
    flex: 1;
}

.chat-name {
    font-weight: 700;
    margin-bottom: 5px;
}

.chat-message {
    font-size: 13px;
    color: var(--dark-500);
}

.chat-time {
    font-size: 11px;
    color: var(--dark-400);
}

/* =============== السوق =============== */
.market-header {
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600), var(--accent-600));
    padding: 20px;
    color: white;
}

.market-header h2 {
    font-size: 24px;
    margin-bottom: 5px;
}

.country-select {
    width: 100%;
    padding: 12px;
    border-radius: var(--radius-full);
    border: none;
    margin-top: 15px;
}

.market-tabs {
    display: flex;
    background: var(--surface-light);
    border-bottom: 1px solid var(--dark-200);
}

.market-tab {
    flex: 1;
    padding: 15px;
    background: none;
    border: none;
    font-size: 15px;
    font-weight: 600;
    color: var(--dark-500);
    cursor: pointer;
}

.market-tab.active {
    color: var(--primary-500);
    border-bottom: 2px solid var(--primary-500);
}

.market-section {
    display: none;
    padding: 15px;
}

.market-section.active {
    display: block;
}

.section-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 15px;
}

.section-header h3 {
    font-size: 18px;
    color: var(--dark-900);
}

body.dark-mode .section-header h3 {
    color: var(--dark-100);
}

.add-store-btn {
    padding: 8px 16px;
    background: linear-gradient(135deg, var(--primary-500), var(--secondary-500));
    border: none;
    border-radius: var(--radius-full);
    color: white;
    font-size: 13px;
    font-weight: 600;
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 6px;
    transition: all var(--transition-normal);
}

.add-store-btn:hover {
    transform: translateY(-2px);
    box-shadow: var(--shadow-md);
}

.stores-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
    margin-bottom: 20px;
}

@media (min-width: 768px) {
    .stores-grid {
        grid-template-columns: repeat(3, 1fr);
    }
}

.store-card {
    background: var(--surface-light);
    border-radius: var(--radius-lg);
    overflow: hidden;
    box-shadow: var(--shadow-sm);
    transition: all var(--transition-normal);
    cursor: pointer;
    border: 1px solid var(--dark-200);
}

body.dark-mode .store-card {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.store-card:hover {
    transform: translateY(-5px);
    box-shadow: var(--shadow-lg);
}

.store-cover {
    height: 80px;
    background: linear-gradient(135deg, var(--primary-400), var(--secondary-400));
    position: relative;
}

.store-logo {
    position: absolute;
    bottom: -25px;
    right: 15px;
    width: 50px;
    height: 50px;
    border-radius: 50%;
    background: white;
    border: 3px solid var(--surface-light);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    box-shadow: var(--shadow-md);
}

.store-info {
    padding: 30px 15px 15px;
}

.store-name {
    font-weight: 600;
    font-size: 16px;
    margin-bottom: 5px;
    color: var(--dark-900);
}

body.dark-mode .store-name {
    color: var(--dark-100);
}

.store-meta {
    display: flex;
    gap: 10px;
    font-size: 11px;
    color: var(--dark-500);
    margin-bottom: 10px;
}

.products-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
}

.product-card {
    background: var(--surface-light);
    border-radius: var(--radius-lg);
    overflow: hidden;
    box-shadow: var(--shadow-sm);
    transition: all var(--transition-normal);
    cursor: pointer;
    border: 1px solid var(--dark-200);
}

body.dark-mode .product-card {
    background: var(--dark-800);
    border-color: var(--dark-700);
}

.product-card:hover {
    transform: translateY(-3px);
    box-shadow: var(--shadow-md);
}

.product-image {
    height: 120px;
    background: linear-gradient(145deg, #f0f0f0, #e0e0e0);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 40px;
    color: var(--dark-400);
}

.product-info {
    padding: 10px;
}

.product-title {
    font-weight: 600;
    font-size: 13px;
    margin-bottom: 5px;
    color: var(--dark-900);
}

body.dark-mode .product-title {
    color: var(--dark-100);
}

.product-price {
    color: var(--primary-600);
    font-weight: 700;
    font-size: 16px;
}

.product-store {
    font-size: 11px;
    color: var(--dark-500);
    margin-top: 5px;
}/* =============== نافذة تسجيل المتجر =============== */
.store-reg-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 9000;
    display: none;
    align-items: center;
    justify-content: center;
}

.store-reg-modal.active {
    display: flex;
}

.store-reg-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0,0,0,0.5);
    backdrop-filter: blur(5px);
}

.store-reg-content {
    position: relative;
    width: 90%;
    max-width: 500px;
    max-height: 85vh;
    overflow-y: auto;
    background: var(--surface-light);
    border-radius: var(--radius-2xl);
    padding: 30px;
    animation: slideUp 0.3s;
    border: 3px solid var(--primary-500);
}

body.dark-mode .store-reg-content {
    background: var(--dark-800);
}

.store-reg-close {
    position: absolute;
    top: 20px;
    left: 20px;
    background: var(--dark-200);
    border: none;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 18px;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all var(--transition-fast);
}

.store-reg-close:hover {
    background: var(--danger-500);
    color: white;
    transform: rotate(90deg);
}

.store-form-group {
    margin-bottom: 20px;
}

.store-form-group label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: var(--dark-800);
}

body.dark-mode .store-form-group label {
    color: var(--dark-200);
}

.store-form-group input,
.store-form-group select,
.store-form-group textarea {
    width: 100%;
    padding: 15px;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-lg);
    font-size: 15px;
    outline: none;
    transition: all var(--transition-fast);
    background: var(--surface-light);
    color: var(--dark-900);
}

body.dark-mode .store-form-group input,
body.dark-mode .store-form-group select,
body.dark-mode .store-form-group textarea {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-100);
}

.store-form-group input:focus,
.store-form-group select:focus,
.store-form-group textarea:focus {
    border-color: var(--primary-500);
    box-shadow: 0 0 0 4px var(--primary-100);
    transform: translateY(-2px);
}

.location-request {
    background: var(--primary-50);
    border-radius: var(--radius-lg);
    padding: 20px;
    margin: 25px 0;
    border: 2px solid var(--primary-500);
}

body.dark-mode .location-request {
    background: var(--dark-700);
}

.location-request h4 {
    font-size: 16px;
    font-weight: 700;
    color: var(--dark-900);
    margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 8px;
}

body.dark-mode .location-request h4 {
    color: var(--dark-100);
}

.location-request h4 i {
    color: var(--danger-500);
}

.location-options {
    display: flex;
    gap: 15px;
    flex-wrap: wrap;
}

.location-option-btn {
    flex: 1;
    padding: 14px;
    background: white;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-lg);
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    font-weight: 600;
    transition: all var(--transition-fast);
}

body.dark-mode .location-option-btn {
    background: var(--dark-800);
    border-color: var(--dark-600);
    color: var(--dark-100);
}

.location-option-btn:hover {
    border-color: var(--primary-500);
    background: var(--primary-50);
    transform: translateY(-2px);
}

.location-option-btn i {
    color: var(--primary-500);
}

.location-status {
    margin-top: 15px;
    padding: 12px;
    background: var(--success-50);
    border-radius: var(--radius-lg);
    color: var(--success-700);
    display: none;
    align-items: center;
    gap: 8px;
    border: 1px solid var(--success-500);
}

.location-status.active {
    display: flex;
}

.subscription-plans {
    margin: 25px 0;
}

.subscription-plans h4 {
    font-size: 18px;
    font-weight: 700;
    color: var(--dark-900);
    margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 8px;
}

body.dark-mode .subscription-plans h4 {
    color: var(--dark-100);
}

.plans-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
}

.plan-option {
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    padding: 15px 10px;
    text-align: center;
    cursor: pointer;
    transition: all var(--transition-fast);
    border: 2px solid transparent;
}

body.dark-mode .plan-option {
    background: var(--dark-700);
}

.plan-option:hover {
    transform: translateY(-3px);
    border-color: var(--primary-500);
}

.plan-option.selected {
    background: linear-gradient(135deg, var(--primary-50), var(--secondary-50));
    border-color: var(--primary-600);
}

body.dark-mode .plan-option.selected {
    background: linear-gradient(135deg, var(--primary-900), var(--secondary-900));
}

.plan-option .plan-name {
    font-weight: 700;
    font-size: 16px;
    color: var(--dark-900);
    margin-bottom: 8px;
}

body.dark-mode .plan-option .plan-name {
    color: var(--dark-100);
}

.plan-option .plan-price {
    font-size: 20px;
    font-weight: 800;
    color: var(--primary-600);
    margin-bottom: 5px;
}

.plan-option .plan-price small {
    font-size: 11px;
    color: var(--dark-500);
}

.store-reg-btn {
    width: 100%;
    padding: 18px;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    border: none;
    border-radius: var(--radius-full);
    font-size: 18px;
    font-weight: 700;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 10px;
    transition: all var(--transition-fast);
    margin-top: 25px;
    box-shadow: 0 10px 20px rgba(79,70,229,0.3);
}

.store-reg-btn:hover {
    transform: translateY(-3px);
    box-shadow: 0 15px 30px rgba(79,70,229,0.4);
}

/* =============== نافذة إنشاء المنشور =============== */
.create-post-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 8000;
    display: none;
    align-items: center;
    justify-content: center;
}

.create-post-modal.active {
    display: flex;
}

.post-modal-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(10px);
}

.post-modal-content {
    position: relative;
    width: 90%;
    max-width: 550px;
    max-height: 85vh;
    background: var(--surface-light);
    border-radius: var(--radius-2xl);
    padding: 25px;
    animation: slideUp 0.4s ease;
    overflow-y: auto;
    border: 3px solid var(--primary-500);
}

body.dark-mode .post-modal-content {
    background: var(--dark-800);
}

.post-modal-header {
    display: flex;
    align-items: center;
    gap: 15px;
    margin-bottom: 20px;
}

.post-modal-avatar {
    width: 55px;
    height: 55px;
    border-radius: 50%;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-weight: 700;
    font-size: 22px;
    border: 3px solid var(--surface-light);
    box-shadow: var(--shadow-lg);
}

body.dark-mode .post-modal-avatar {
    border-color: var(--dark-800);
}

.post-modal-user-info {
    flex: 1;
}

.post-modal-username {
    font-weight: 700;
    font-size: 18px;
    color: var(--dark-900);
    margin-bottom: 5px;
}

body.dark-mode .post-modal-username {
    color: var(--dark-100);
}

.post-modal-time {
    font-size: 12px;
    color: var(--dark-500);
    display: flex;
    align-items: center;
    gap: 5px;
}

.post-modal-close {
    background: var(--dark-200);
    border: none;
    width: 45px;
    height: 45px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 20px;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all var(--transition-fast);
}

.post-modal-close:hover {
    background: var(--danger-500);
    color: white;
    transform: rotate(90deg);
}

.post-modal-input {
    width: 100%;
    padding: 18px;
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-lg);
    font-size: 16px;
    line-height: 1.7;
    resize: none;
    outline: none;
    transition: all var(--transition-fast);
    margin-bottom: 15px;
    background: var(--surface-light);
    color: var(--dark-900);
    min-height: 100px;
}

body.dark-mode .post-modal-input {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-100);
}

.post-modal-input:focus {
    border-color: var(--primary-500);
    box-shadow: 0 0 0 4px var(--primary-100);
    transform: translateY(-2px);
}

.post-modal-counter {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 15px;
    padding: 8px 15px;
    background: var(--dark-100);
    border-radius: var(--radius-full);
    border: 1px solid var(--dark-200);
}

body.dark-mode .post-modal-counter {
    background: var(--dark-700);
    border-color: var(--dark-600);
}

.counter-text {
    font-size: 14px;
    font-weight: 600;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    gap: 6px;
}

.counter-text i {
    color: var(--primary-500);
}

.counter-progress {
    width: 120px;
    height: 8px;
    background: var(--dark-300);
    border-radius: var(--radius-full);
    overflow: hidden;
}

.counter-bar {
    height: 100%;
    background: linear-gradient(90deg, var(--primary-500), var(--secondary-500));
    width: 0%;
    transition: width 0.2s ease;
}

.post-media-tools {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 10px;
    margin: 15px 0;
}

.media-tool-btn {
    background: var(--dark-100);
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-lg);
    padding: 15px 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    cursor: pointer;
    transition: all var(--transition-fast);
    color: var(--dark-700);
    font-weight: 600;
}

body.dark-mode .media-tool-btn {
    background: var(--dark-700);
    border-color: var(--dark-600);
    color: var(--dark-300);
}

.media-tool-btn:hover {
    transform: translateY(-3px);
    border-color: var(--primary-500);
    background: var(--primary-50);
    color: var(--primary-700);
}

.media-tool-btn i {
    font-size: 24px;
    color: var(--primary-500);
}

.media-tool-btn:nth-child(1) i { color: var(--primary-500); }
.media-tool-btn:nth-child(2) i { color: var(--accent-500); }

/* =============== واجهة تحرير الفيديو العمودية =============== */
.video-editor-area {
    display: flex;
    gap: 15px;
    margin: 20px 0;
    padding: 15px;
    background: var(--dark-100);
    border-radius: var(--radius-xl);
    border: 2px solid var(--primary-200);
}

.video-preview-container {
    flex: 1;
    position: relative;
    background: #000;
    border-radius: var(--radius-lg);
    overflow: hidden;
    aspect-ratio: 9/16;
    max-width: 180px;
}

.video-preview-container video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.video-overlay-layer {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
}

.video-tools-vertical {
    display: flex;
    flex-direction: column;
    gap: 8px;
    width: 70px;
}

.tool-btn {
    background: white;
    border: 1px solid var(--dark-200);
    border-radius: var(--radius-lg);
    padding: 10px 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4px;
    cursor: pointer;
    transition: all 0.2s;
    color: var(--dark-700);
    font-size: 11px;
}

.tool-btn:hover {
    background: var(--primary-50);
    border-color: var(--primary-500);
    color: var(--primary-700);
    transform: translateX(-3px);
}

.tool-btn i {
    font-size: 18px;
    color: var(--primary-500);
}

.draggable-text {
    position: absolute;
    color: white;
    font-size: 18px;
    font-weight: bold;
    text-shadow: 2px 2px 4px black;
    cursor: move;
    user-select: none;
    background: rgba(0,0,0,0.6);
    padding: 5px 10px;
    border-radius: 20px;
    border: 1px solid white;
    pointer-events: auto;
    z-index: 10;
}

.filter-panel {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: white;
    border-radius: var(--radius-2xl);
    padding: 20px;
    box-shadow: var(--shadow-2xl);
    z-index: 11000;
    display: none;
    width: 250px;
}

.filter-panel.active {
    display: block;
}

.filter-option {
    padding: 10px;
    margin: 5px 0;
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    cursor: pointer;
    text-align: center;
}

.filter-option:hover {
    background: var(--primary-100);
}

.post-media-preview {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
    gap: 10px;
    margin: 15px 0;
    min-height: 90px;
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    padding: 12px;
    border: 2px dashed var(--dark-300);
}

.preview-item {
    position: relative;
    aspect-ratio: 1;
    border-radius: var(--radius-md);
    overflow: hidden;
    cursor: pointer;
    border: 2px solid transparent;
}

.preview-item:hover {
    border-color: var(--primary-500);
    transform: scale(1.05);
}

.preview-item img,
.preview-item video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.preview-remove {
    position: absolute;
    top: 3px;
    right: 3px;
    width: 22px;
    height: 22px;
    border-radius: 50%;
    background: var(--danger-500);
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 12px;
    cursor: pointer;
    opacity: 0;
    transition: opacity 0.2s;
    border: 2px solid white;
}

.preview-item:hover .preview-remove {
    opacity: 1;
}.post-modal-location {
    margin: 15px 0;
}

.location-toggle {
    width: 100%;
    padding: 14px;
    background: var(--dark-100);
    border: 2px solid var(--dark-200);
    border-radius: var(--radius-full);
    display: flex;
    align-items: center;
    gap: 10px;
    cursor: pointer;
    transition: all var(--transition-fast);
    color: var(--dark-700);
    font-weight: 600;
}

.location-toggle:hover {
    border-color: var(--primary-500);
    background: var(--primary-50);
    color: var(--primary-700);
    transform: translateY(-2px);
}

.location-toggle i {
    color: var(--primary-500);
    font-size: 18px;
}

.location-selector {
    margin-top: 10px;
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    padding: 15px;
    border: 1px solid var(--dark-200);
    display: none;
}

.location-selector.active {
    display: block;
}

.location-item {
    padding: 12px;
    border-radius: var(--radius-lg);
    cursor: pointer;
    display: flex;
    align-items: center;
    gap: 10px;
    transition: all var(--transition-fast);
    border-bottom: 1px solid var(--dark-200);
}

.location-item:last-child {
    border-bottom: none;
}

.location-item:hover {
    background: var(--surface-light);
    transform: translateX(-5px);
}

.location-item i {
    color: var(--primary-500);
    width: 20px;
}

.promote-post-option {
    background: linear-gradient(135deg, var(--primary-50), var(--secondary-50));
    border-radius: var(--radius-lg);
    padding: 20px;
    margin: 15px 0;
    border: 2px solid var(--primary-500);
    cursor: pointer;
    transition: all var(--transition-fast);
}

.promote-post-option:hover {
    transform: translateY(-3px);
    box-shadow: var(--shadow-xl);
}

.promote-header {
    display: flex;
    align-items: center;
    gap: 10px;
}

.promote-header i {
    color: #ffd700;
    font-size: 24px;
}

.promote-header h4 {
    font-size: 16px;
    font-weight: 700;
    color: var(--dark-900);
}

body.dark-mode .promote-header h4 {
    color: var(--dark-100);
}

.post-modal-actions {
    display: flex;
    gap: 15px;
    margin-top: 20px;
}

.post-modal-publish {
    flex: 1;
    padding: 16px;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
    border: none;
    border-radius: var(--radius-full);
    font-size: 18px;
    font-weight: 700;
    cursor: pointer;
    transition: all var(--transition-fast);
    box-shadow: 0 8px 16px rgba(79,70,229,0.3);
}

.post-modal-publish:hover:not(:disabled) {
    transform: translateY(-3px);
    box-shadow: 0 12px 24px rgba(79,70,229,0.4);
}

.post-modal-publish:disabled {
    opacity: 0.5;
    cursor: not-allowed;
}

.post-modal-draft {
    padding: 16px 25px;
    background: var(--dark-200);
    color: var(--dark-700);
    border: none;
    border-radius: var(--radius-full);
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all var(--transition-fast);
}

.post-modal-draft:hover {
    background: var(--dark-300);
    transform: translateY(-2px);
}

/* =============== نافذة الترويج (معدلة) =============== */
.promo-modal {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 9500;
    display: none;
    align-items: center;
    justify-content: center;
}

.promo-modal.active {
    display: flex;
}

.promo-overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0,0,0,0.7);
    backdrop-filter: blur(5px);
}

.promo-content {
    position: relative;
    width: 90%;
    max-width: 500px;
    max-height: 85vh;
    overflow-y: auto;
    background: var(--surface-light);
    border-radius: var(--radius-2xl);
    padding: 25px;
    animation: slideUp 0.3s;
    border: 3px solid var(--primary-500);
}

body.dark-mode .promo-content {
    background: var(--dark-800);
}

.promo-header {
    text-align: center;
    margin-bottom: 20px;
}

.promo-header h2 {
    font-size: 24px;
    font-weight: 800;
    color: var(--dark-900);
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
}

body.dark-mode .promo-header h2 {
    color: var(--dark-100);
}

.promo-header h2 i {
    color: #ffd700;
}

.promo-close {
    position: absolute;
    top: 15px;
    left: 15px;
    background: var(--dark-200);
    border: none;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    cursor: pointer;
    font-size: 16px;
    color: var(--dark-700);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s;
}

.promo-close:hover {
    background: var(--danger-500);
    color: white;
    transform: rotate(90deg);
}

.promo-plans {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
    gap: 10px;
    margin: 20px 0;
}

.promo-plan {
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    padding: 15px 10px;
    text-align: center;
    cursor: pointer;
    transition: all 0.2s;
    border: 2px solid transparent;
}

body.dark-mode .promo-plan {
    background: var(--dark-700);
}

.promo-plan:hover {
    transform: translateY(-3px);
    border-color: var(--primary-500);
}

.promo-plan.selected {
    background: var(--primary-50);
    border-color: var(--primary-600);
}

body.dark-mode .promo-plan.selected {
    background: var(--primary-900);
}

.promo-plan .plan-name {
    font-weight: 700;
    font-size: 16px;
    margin-bottom: 5px;
}

.promo-plan .plan-price {
    font-size: 20px;
    font-weight: 800;
    color: var(--primary-600);
}

.promo-plan .plan-price small {
    font-size: 11px;
    color: var(--dark-500);
    text-decoration: line-through;
    margin-left: 5px;
}

.promo-goals {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin: 20px 0;
    justify-content: center;
}

.goal-item {
    flex: 1 1 100px;
    text-align: center;
    padding: 10px;
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    cursor: pointer;
    transition: all 0.2s;
    border: 2px solid transparent;
}

.goal-item:hover {
    transform: translateY(-2px);
    border-color: var(--primary-500);
}

.goal-item.active {
    background: var(--primary-50);
    border-color: var(--primary-600);
}

.goal-item i {
    font-size: 20px;
    display: block;
    margin-bottom: 5px;
    color: var(--primary-500);
}

.goal-item span {
    font-size: 12px;
    font-weight: 600;
}

.promo-total {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 15px;
    background: var(--primary-50);
    border-radius: var(--radius-lg);
    margin: 20px 0;
    font-weight: 700;
}

body.dark-mode .promo-total {
    background: var(--dark-700);
}

.promo-total span:last-child {
    color: var(--primary-600);
    font-size: 20px;
}

.promo-confirm-btn {
    width: 100%;
    padding: 16px;
    background: linear-gradient(135deg, #ffd700, #ffb347);
    color: #000;
    border: none;
    border-radius: var(--radius-full);
    font-weight: 800;
    font-size: 16px;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    transition: all 0.2s;
}

.promo-confirm-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 15px rgba(255,215,0,0.4);
}

/* =============== نافذة العرض الكامل للفيديو =============== */
.reels-fullscreen {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: #000;
    z-index: 9000;
    display: none;
    flex-direction: column;
}

.reels-fullscreen.active {
    display: flex;
}

.reels-container {
    flex: 1;
    scroll-snap-type: y mandatory;
    overflow-y: scroll;
}

.reel-item {
    height: 100%;
    width: 100%;
    scroll-snap-align: start;
    position: relative;
}

.reel-item video {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

.reels-close {
    position: absolute;
    top: 20px;
    left: 20px;
    background: rgba(0,0,0,0.5);
    border: none;
    color: white;
    width: 45px;
    height: 45px;
    border-radius: 50%;
    font-size: 24px;
    z-index: 10;
    cursor: pointer;
}

/* =============== الملف الشخصي =============== */
.profile-page {
    background: var(--dark-100);
    min-height: 100%;
}

.profile-cover {
    height: 140px;
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600), var(--accent-600));
    position: relative;
}

.profile-avatar-container {
    position: relative;
    width: fit-content;
    margin: -60px 20px 0;
}

.profile-avatar-large {
    width: 120px;
    height: 120px;
    border-radius: 50%;
    border: 5px solid var(--surface-light);
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 48px;
    color: white;
}

.edit-avatar-btn {
    position: absolute;
    bottom: 10px;
    right: 10px;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background: var(--primary-600);
    color: white;
    border: 3px solid var(--surface-light);
    cursor: pointer;
}

.profile-name {
    font-size: 26px;
    font-weight: 800;
}

.edit-name-btn {
    background: none;
    border: none;
    color: var(--dark-500);
    font-size: 20px;
    cursor: pointer;
}

.profile-bio {
    padding: 15px;
    background: var(--surface-light);
    border-radius: var(--radius-lg);
    margin: 0 20px 20px;
    position: relative;
}

.edit-bio-btn {
    position: absolute;
    bottom: 10px;
    left: 10px;
    background: var(--dark-200);
    border: none;
    width: 30px;
    height: 30px;
    border-radius: 50%;
    cursor: pointer;
}

.stats-container {
    background: var(--surface-light);
    border-radius: var(--radius-xl);
    padding: 20px;
    margin: 20px;
}

.stats-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
}

.stat-card {
    text-align: center;
}

.stat-value {
    font-size: 22px;
    font-weight: 800;
    color: var(--dark-900);
}

.account-status {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px 15px;
    background: var(--dark-100);
    border-radius: var(--radius-lg);
    margin: 15px 20px;
}

.status-badge {
    padding: 5px 12px;
    border-radius: var(--radius-full);
    font-size: 13px;
}

.status-badge.public {
    background: var(--success-100);
    color: var(--success-700);
}

.edit-status-btn {
    margin-right: auto;
    background: none;
    border: none;
    color: var(--primary-600);
    cursor: pointer;
}

.action-buttons {
    display: flex;
    gap: 12px;
    margin: 20px;
}

.action-btn {
    flex: 1;
    padding: 14px;
    border: none;
    border-radius: var(--radius-lg);
    font-weight: 700;
    cursor: pointer;
}

.action-btn.primary {
    background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
    color: white;
}

.action-btn.secondary {
    background: var(--surface-light);
    color: var(--dark-800);
    border: 2px solid var(--dark-200);
}

.profile-tabs {
    display: flex;
    border-top: 2px solid var(--dark-200);
    margin: 20px 0;
}

.profile-tab {
    flex: 1;
    padding: 15px;
    text-align: center;
    cursor: pointer;
    font-weight: 700;
}

.profile-tab.active {
    color: var(--primary-600);
    border-bottom: 3px solid var(--primary-600);
}

.profile-posts-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2px;
}<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes">
    <title>نوفالكس - المنصة المتكاملة</title>
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* =============== المتغيرات والأنماط العامة =============== */
        :root {
            --primary-50: #eef2ff;
            --primary-100: #e0e7ff;
            --primary-500: #6366f1;
            --primary-600: #4f46e5;
            --primary-700: #4338ca;
            --secondary-500: #8b5cf6;
            --secondary-600: #7c3aed;
            --accent-500: #f59e0b;
            --accent-600: #d97706;
            --success-500: #10b981;
            --success-600: #059669;
            --danger-500: #ef4444;
            --danger-600: #dc2626;
            --warning-500: #f59e0b;
            --warning-600: #d97706;
            --info-500: #3b82f6;
            --info-600: #2563eb;
            --dark-100: #f3f4f6;
            --dark-200: #e5e7eb;
            --dark-300: #d1d5db;
            --dark-500: #6b7280;
            --dark-600: #4b5563;
            --dark-700: #374151;
            --dark-800: #1f2937;
            --dark-900: #111827;
            --surface-light: #ffffff;
            --surface-dark: #1f2937;
            --radius-sm: 4px;
            --radius-md: 8px;
            --radius-lg: 12px;
            --radius-xl: 16px;
            --radius-2xl: 24px;
            --radius-full: 9999px;
            --shadow-sm: 0 1px 2px 0 rgb(0 0 0 / 0.05);
            --shadow-md: 0 4px 6px -1px rgb(0 0 0 / 0.1);
            --shadow-lg: 0 10px 15px -3px rgb(0 0 0 / 0.1);
            --shadow-xl: 0 20px 25px -5px rgb(0 0 0 / 0.1);
            --shadow-2xl: 0 25px 50px -12px rgb(0 0 0 / 0.25);
            --transition-fast: 150ms;
            --transition-normal: 250ms;
            --transition-slow: 350ms;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: var(--dark-100);
            color: var(--dark-900);
            transition: background-color var(--transition-normal);
            height: 100vh;
            overflow: hidden;
        }

        body.dark-mode {
            background: var(--dark-900);
            color: var(--dark-100);
        }

        /* إطارات الهاتف - متجاوبة */
        .phone-frame {
            max-width: 400px;
            width: 100%;
            margin: 0 auto;
            background: var(--surface-light);
            height: 100vh;
            overflow: hidden;
            position: relative;
            box-shadow: var(--shadow-2xl);
        }

        body.dark-mode .phone-frame {
            background: var(--dark-800);
        }

        .app {
            display: flex;
            flex-direction: column;
            height: 100%;
            overflow: hidden;
            position: relative;
        }

        /* =============== الهيدر =============== */
        .header {
            flex-shrink: 0;
            height: 60px;
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white;
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 0 20px;
            box-shadow: var(--shadow-md);
            z-index: 1000;
        }

        .logo {
            font-size: 24px;
            font-weight: 800;
            letter-spacing: -0.5px;
            cursor: pointer;
        }

        .header-actions {
            display: flex;
            gap: 15px;
        }

        .header-btn {
            background: rgba(255,255,255,0.2);
            border: none;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
            position: relative;
            transition: all var(--transition-fast);
        }

        .header-btn:hover {
            background: rgba(255,255,255,0.3);
            transform: scale(1.05);
        }

        .badge[data-count]:after {
            content: attr(data-count);
            position: absolute;
            top: -5px;
            right: -5px;
            background: var(--danger-500);
            color: white;
            font-size: 10px;
            font-weight: 600;
            padding: 2px 6px;
            border-radius: var(--radius-full);
            min-width: 16px;
        }

        /* =============== المحتوى الرئيسي =============== */
        .main-content {
            flex: 1;
            overflow-y: auto;
            padding-bottom: 70px;
            scroll-behavior: smooth;
            -webkit-overflow-scrolling: touch;
        }

        .page {
            display: none;
            height: 100%;
        }

        .page.active-page {
            display: block;
        }

        /* =============== الإشعارات =============== */
        .notification-center {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: var(--surface-light);
            z-index: 8500;
            transform: translateX(-100%);
            transition: transform var(--transition-normal);
            display: flex;
            flex-direction: column;
        }

        .notification-center.active {
            transform: translateX(0);
        }

        .notification-header {
            padding: 20px;
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .notification-back-btn {
            background: rgba(255,255,255,0.2);
            border: none;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
        }

        .notification-list {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
        }

        .notification-item {
            display: flex;
            align-items: center;
            gap: 15px;
            padding: 15px;
            background: var(--dark-100);
            border-radius: var(--radius-xl);
            margin-bottom: 12px;
            border: 1px solid var(--dark-200);
        }

        .notification-item.unread {
            background: var(--primary-50);
            border-right: 5px solid var(--primary-500);
        }

        .notification-icon {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            background: var(--primary-100);
            display: flex;
            align-items: center;
            justify-content: center;
            color: var(--primary-600);
            font-size: 22px;
        }

        /* =============== شريط التنقل السفلي =============== */
        .bottom-nav {
            flex-shrink: 0;
            height: 70px;
            background: var(--surface-light);
            display: flex;
            justify-content: space-around;
            align-items: center;
            border-top: 2px solid var(--dark-200);
            width: 100%;
            padding: 5px 0;
            box-shadow: 0 -4px 15px rgba(0,0,0,0.05);
            z-index: 100;
            position: absolute;
            bottom: 0;
            left: 0;
            right: 0;
            max-width: 400px;
            margin: 0 auto;
        }

        body.dark-mode .bottom-nav {
            background: var(--dark-800);
            border-color: var(--dark-700);
        }

        .nav-item {
            background: none;
            border: none;
            font-size: 20px;
            color: var(--dark-500);
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 3px;
            padding: 5px 2px;
            border-radius: var(--radius-full);
            min-width: 44px;
            position: relative;
            transition: all var(--transition-fast);
        }

        .nav-item span {
            font-size: 10px;
            font-weight: 600;
        }

        .nav-item:hover {
            color: var(--primary-600);
            transform: translateY(-2px);
        }

        .nav-item.active {
            color: var(--primary-600);
        }

        .nav-item.active::before {
            content: '';
            position: absolute;
            bottom: -2px;
            width: 20px;
            height: 3px;
            background: var(--primary-600);
            border-radius: var(--radius-full) var(--radius-full) 0 0;
        }

        .publish-btn i {
            color: var(--primary-600);
            font-size: 24px;
            animation: float 3s ease-in-out infinite;
        }

        @keyframes float {
            0%,100% { transform: translateY(0); }
            50% { transform: translateY(-3px); }
        }

        /* =============== مساعدات =============== */
        .toast {
            position: fixed;
            bottom: 90px;
            left: 50%;
            transform: translateX(-50%);
            background: var(--dark-900);
            color: white;
            padding: 12px 24px;
            border-radius: var(--radius-full);
            font-size: 14px;
            z-index: 10000;
            box-shadow: var(--shadow-2xl);
            animation: slideUp 0.3s;
            display: flex;
            align-items: center;
            gap: 8px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255,255,255,0.2);
        }

        @keyframes slideUp {
            from { transform: translateX(-50%) translateY(100px); opacity: 0; }
            to { transform: translateX(-50%) translateY(0); opacity: 1; }
        }

        .toast.success {
            background: linear-gradient(135deg, var(--success-600), var(--success-500));
        }

        .toast.error {
            background: linear-gradient(135deg, var(--danger-600), var(--danger-500));
        }

        .toast.warning {
            background: linear-gradient(135deg, var(--warning-600), var(--warning-500));
        }

        .toast i {
            font-size: 18px;
        }

        .hidden {
            display: none !important;
        }

        /* =============== القصص =============== */
        .stories-section {
            background: var(--surface-light);
            padding: 15px 0;
            overflow-x: auto;
            overflow-y: hidden;
            white-space: nowrap;
            margin-bottom: 10px;
            width: 100%;
            border-bottom: 1px solid var(--dark-200);
            -webkit-overflow-scrolling: touch;
            scrollbar-width: none;
        }

        .stories-section::-webkit-scrollbar {
            display: none;
        }

        .stories-container {
            display: inline-flex;
            gap: 18px;
            padding: 0 20px;
            flex-direction: row;
        }

        .story-item {
            display: inline-flex;
            flex-direction: column;
            align-items: center;
            gap: 6px;
            width: 75px;
            cursor: pointer;
            transition: all var(--transition-fast);
            text-align: center;
        }

        .story-item:hover {
            transform: translateY(-3px);
        }

        .story-ring {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            background: linear-gradient(45deg, var(--primary-600), var(--secondary-600), var(--accent-600));
            padding: 3px;
            display: flex;
            align-items: center;
            justify-content: center;
            animation: rotate 10s linear infinite;
        }

        .story-avatar {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            border: 3px solid var(--surface-light);
            background: var(--dark-200);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 30px;
            font-weight: 600;
            color: var(--dark-700);
            transition: all var(--transition-fast);
            object-fit: cover;
        }

        body.dark-mode .story-avatar {
            border-color: var(--dark-800);
            background: var(--dark-700);
            color: var(--dark-300);
        }

        .story-add {
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white !important;
        }

        .story-name {
            font-size: 12px;
            font-weight: 600;
            max-width: 70px;
            overflow: hidden;
            text-overflow: ellipsis;
            color: var(--dark-700);
            white-space: nowrap;
        }

        body.dark-mode .story-name {
            color: var(--dark-300);
        }

        /* =============== أنماط المتجر =============== */
        .store-detail-header {
            padding: 20px;
            background: var(--surface-light);
            border-bottom: 1px solid var(--dark-200);
            display: flex;
            align-items: center;
            gap: 15px;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .store-detail-back {
            background: var(--dark-100);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
            color: var(--dark-700);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .store-detail-info {
            flex: 1;
            display: flex;
            align-items: center;
            gap: 15px;
        }
        .store-detail-logo {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--primary-100), var(--secondary-100));
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 36px;
        }
        .store-detail-info h2 {
            font-size: 20px;
            font-weight: 700;
        }
        .store-detail-info p {
            color: var(--dark-500);
            font-size: 13px;
        }
        .manage-store-btn {
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white;
            border: none;
            border-radius: var(--radius-full);
            padding: 10px 20px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .store-products-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            padding: 15px;
        }
        .store-product-card {
            background: var(--surface-light);
            border-radius: var(--radius-lg);
            overflow: hidden;
            border: 1px solid var(--dark-200);
            cursor: pointer;
            transition: all 0.2s;
        }
        .store-product-card:hover {
            transform: translateY(-3px);
            box-shadow: var(--shadow-lg);
        }
        .store-product-image {
            height: 120px;
            background: var(--dark-100);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 48px;
        }
        .store-product-info {
            padding: 10px;
        }
        .store-product-title {
            font-weight: 600;
            font-size: 14px;
            margin-bottom: 5px;
        }
        .store-product-price {
            color: var(--primary-600);
            font-weight: 700;
        }

        /* =============== صفحة إدارة المتجر =============== */
        .manage-header {
            padding: 20px;
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white;
            display: flex;
            align-items: center;
            gap: 15px;
        }
        .manage-back {
            background: rgba(255,255,255,0.2);
            border: none;
            color: white;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .manage-stats {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            padding: 15px;
        }
        .manage-stat-card {
            background: var(--surface-light);
            border-radius: var(--radius-lg);
            padding: 15px;
            text-align: center;
            border: 1px solid var(--dark-200);
        }
        .manage-stat-value {
            display: block;
            font-size: 20px;
            font-weight: 800;
            color: var(--primary-600);
        }
        .manage-stat-label {
            font-size: 12px;
            color: var(--dark-500);
        }
        .manage-actions {
            display: flex;
            gap: 10px;
            padding: 0 15px 15px;
        }
        .manage-action-btn {
            flex: 1;
            padding: 12px;
            background: var(--dark-100);
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-lg);
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 8px;
        }
        .manage-action-btn:hover {
            background: var(--primary-50);
            border-color: var(--primary-500);
            color: var(--primary-700);
        }
        .manage-products-list {
            padding: 15px;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .manage-product-item {
            background: var(--surface-light);
            border-radius: var(--radius-lg);
            padding: 12px;
            display: flex;
            align-items: center;
            gap: 15px;
            border: 1px solid var(--dark-200);
        }
        .manage-product-image {
            width: 50px;
            height: 50px;
            border-radius: var(--radius-md);
            background: var(--dark-100);
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
        }
        .manage-product-details {
            flex: 1;
        }
        .manage-product-name {
            font-weight: 600;
            margin-bottom: 5px;
        }
        .manage-product-price {
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .manage-price-input {
            width: 80px;
            padding: 5px;
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-md);
            font-size: 14px;
        }
        .manage-save-price {
            background: none;
            border: none;
            color: var(--primary-600);
            cursor: pointer;
            font-size: 16px;
        }
        .manage-delete-product {
            background: none;
            border: none;
            color: var(--danger-500);
            cursor: pointer;
            font-size: 18px;
        }

        /* =============== أنماط جديدة للإضافات =============== */
        .post-options {
            padding: 10px 20px;
            border-top: 1px solid var(--dark-200);
            margin-top: 10px;
        }
        .option-checkbox {
            display: flex;
            align-items: center;
            gap: 8px;
            cursor: pointer;
            color: var(--dark-700);
            font-size: 14px;
        }
        .option-checkbox input {
            width: 18px;
            height: 18px;
        }
        .feed-action.disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }
        .post-context-menu {
            position: absolute;
            background: var(--surface-light);
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-lg);
            box-shadow: var(--shadow-xl);
            z-index: 1000;
            min-width: 180px;
        }
        .post-context-menu ul {
            list-style: none;
            padding: 8px 0;
            margin: 0;
        }
        .post-context-menu li {
            padding: 10px 16px;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 10px;
            font-size: 14px;
        }
        .post-context-menu li:hover {
            background: var(--dark-100);
        }
        .action-btn.danger {
            background: var(--danger-100);
            color: var(--danger-700);
            border: 1px solid var(--danger-300);
        }
        .action-btn.danger:hover {
            background: var(--danger-200);
        }
        .promo-days, .promo-price-input {
            padding: 10px 20px;
            display: flex;
            flex-direction: column;
            gap: 5px;
        }
        .promo-days label, .promo-price-input label {
            font-weight: 600;
            font-size: 14px;
        }
        .promo-days input, .promo-price-input input {
            padding: 8px;
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-md);
        }
        .price-hint {
            font-size: 11px;
            color: var(--dark-500);
        }

        /* تحسينات للهواتف - نافذة التعليقات */
        @media (max-width: 480px) {
            .phone-frame {
                max-width: 100%;
            }
            .bottom-nav {
                max-width: 100%;
            }
        }

        /* نافذة التعليقات - تصميم متجاوب للهواتف */
        .comments-modal {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            z-index: 9000;
            display: none;
            align-items: flex-end;
        }
        .comments-modal.active {
            display: flex !important;
        }
        .comments-overlay {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.5);
        }
        .comments-content {
            position: relative;
            width: 100%;
            max-height: 90vh;
            background: var(--surface-light);
            border-radius: 20px 20px 0 0;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            animation: slideUp 0.3s ease;
        }
        @keyframes slideUp {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }
        .comments-header {
            padding: 15px;
            border-bottom: 1px solid var(--dark-200);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .comments-list {
            flex: 1;
            overflow-y: auto;
            padding: 15px;
            max-height: calc(90vh - 120px);
        }
        .comment-input-area {
            display: flex;
            padding: 10px;
            border-top: 1px solid var(--dark-200);
            background: var(--surface-light);
            position: sticky;
            bottom: 0;
        }
        .comment-input {
            flex: 1;
            padding: 10px;
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-full);
            margin-left: 10px;
            font-size: 16px;
        }
        .comment-send {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            background: var(--primary-600);
            color: white;
            border: none;
            cursor: pointer;
        }

        /* =============== أنماط ملف الشخص الآخر =============== */
        .user-profile-header {
            padding: 20px;
            background: var(--surface-light);
            border-bottom: 1px solid var(--dark-200);
            display: flex;
            align-items: center;
            gap: 15px;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        .user-profile-back {
            background: var(--dark-100);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            font-size: 18px;
            cursor: pointer;
            color: var(--dark-700);
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .user-profile-info {
            flex: 1;
            display: flex;
            align-items: center;
            gap: 15px;
        }
        .user-profile-avatar {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--primary-100), var(--secondary-100));
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 36px;
            font-weight: 600;
            color: var(--primary-700);
        }
        .user-profile-details h2 {
            font-size: 20px;
            font-weight: 700;
        }
        .user-profile-details p {
            color: var(--dark-500);
            font-size: 13px;
        }
        .message-user-btn {
            background: linear-gradient(135deg, var(--primary-600), var(--secondary-600));
            color: white;
            border: none;
            border-radius: var(--radius-full);
            padding: 10px 20px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .user-profile-menu-btn {
            background: none;
            border: none;
            font-size: 24px;
            color: var(--dark-700);
            width: 40px;
            height: 40px;
            border-radius: 50%;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .user-profile-menu-btn:hover {
            background: var(--dark-100);
        }

        /* إحصائيات الملف الشخصي */
        .profile-stats {
            display: flex;
            justify-content: space-around;
            padding: 20px;
            border-bottom: 1px solid var(--dark-200);
        }
        .profile-stat {
            text-align: center;
        }
        .profile-stat-value {
            font-size: 20px;
            font-weight: 800;
            color: var(--primary-600);
        }
        .profile-stat-label {
            font-size: 12px;
            color: var(--dark-500);
        }

        /* أنماط صفحة إدارة البلاغات */
        .report-item {
            background: var(--surface-light);
            border: 1px solid var(--dark-200);
            border-radius: var(--radius-lg);
            padding: 15px;
            margin-bottom: 10px;
        }
        .report-status {
            background: var(--warning-100);
            color: var(--warning-700);
            padding: 4px 8px;
            border-radius: var(--radius-full);
            font-size: 12px;
        }
        .report-actions {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }
        .report-btn {
            border: none;
            padding: 8px 16px;
            border-radius: var(--radius-full);
            cursor: pointer;
            font-size: 14px;
        }
        .report-btn.success {
            background: var(--success-500);
            color: white;
        }
        .report-btn.danger {
            background: var(--danger-500);
            color: white;
        }
    </style>
</head>
<body>
    <div class="phone-frame">
        <div class="app">
            <!-- شاشة تسجيل الدخول -->
            <div class="auth-screen" id="authScreen">
                <div class="auth-card">
                    <div class="auth-logo">Novalex</div>
                    <input type="email" class="auth-input" id="loginEmail" placeholder="البريد الإلكتروني" value="test@novalex.com">
                    <input type="password" class="auth-input" id="loginPassword" placeholder="كلمة المرور" value="123456">
                    <button class="auth-btn" onclick="AuthSystem.login()">تسجيل الدخول</button>
                    <div class="auth-switch" onclick="AuthSystem.showRegister()">إنشاء حساب جديد</div>
                </div>
            </div>

            <!-- الهيدر -->
            <header class="header" id="mainHeader">
                <div class="logo" onclick="Navigation.switchPage('home')">Novalex</div>
                <div class="header-actions">
                    <button class="header-btn" onclick="Navigation.switchPage('chat')" title="الدردشة">
                        <i class="fas fa-comment-dots"></i>
                    </button>
                    <button class="header-btn badge" data-count="3" onclick="NotificationSystem.show()" title="الإشعارات">
                        <i class="fas fa-bell"></i>
                    </button>
                    <button class="header-btn" onclick="Settings.openSettings()" title="الإعدادات">
                        <i class="fas fa-cog"></i>
                    </button>
                </div>
            </header>

            <!-- المحتوى الرئيسي -->
            <main class="main-content" id="mainContent">
                <!-- الصفحة الرئيسية -->
                <div id="homePage" class="page active-page">
                    <div class="stories-section">
                        <div class="stories-container" id="storiesContainer"></div>
                    </div>
                    
                    <div class="create-post">
                        <div class="create-post-header">
                            <div class="user-avatar" id="currentUserAvatar">م</div>
                            <textarea class="post-input" id="postContent" placeholder="ما الذي تفكر فيه؟ (280 حرف)" rows="2" maxlength="280" oninput="PostSystem.updateCounter(this)"></textarea>
                        </div>
                        
                        <div class="post-char-counter" id="postCharCounter" style="display: none;">
                            <span class="post-char-text">
                                <i class="fas fa-text-height"></i>
                                <span id="postCharText">0/280</span>
                            </span>
                            <div class="post-char-progress">
                                <div class="post-char-bar" id="postCharBar" style="width: 0%;"></div>
                            </div>
                        </div>
                        
                        <div class="post-media-actions">
                            <button class="post-media-btn" onclick="PostModal.openWithImage()">
                                <i class="fas fa-image"></i> صورة
                            </button>
                            <button class="post-media-btn" onclick="PostModal.openWithVideo()">
                                <i class="fas fa-video"></i> فيديو
                            </button>
                            <button class="post-media-btn" onclick="PostModal.open()">
                                <i class="fas fa-pen"></i> نص
                            </button>
                        </div>
                    </div>

                    <div class="main-feed" id="mainFeed"></div>
                </div>

                <!-- صفحة البث المباشر -->
                <div id="livePage" class="page">
                    <div class="live-header">
                        <h2><i class="fas fa-circle"></i> البث المباشر</h2>
                        <button class="start-live-btn" onclick="LiveSystem.startLive()">
                            <i class="fas fa-video"></i> بدء بث
                        </button>
                    </div>
                    <div class="live-grid" id="liveGrid"></div>
                </div>

                <!-- صفحة البحث -->
                <div id="searchPage" class="page">
                    <div class="search-bar">
                        <input type="text" class="search-input" id="searchInput" placeholder="بحث...">
                        <button class="search-btn" onclick="SearchSystem.performSearch()"><i class="fas fa-search"></i></button>
                    </div>
                    <div class="search-filters">
                        <span class="filter-chip active" onclick="SearchSystem.filter('all', this)">الكل</span>
                        <span class="filter-chip" onclick="SearchSystem.filter('people', this)">أشخاص</span>
                        <span class="filter-chip" onclick="SearchSystem.filter('videos', this)">فيديوهات</span>
                        <span class="filter-chip" onclick="SearchSystem.filter('live', this)">مباشر</span>
                    </div>
                    <div class="search-results" id="searchResults"></div>
                </div>

                <!-- صفحة الدردشة (المحادثات) -->
                <div id="chatPage" class="page">
                    <div class="chat-header" style="padding:15px; border-bottom:1px solid var(--dark-200);">
                        <h2><i class="fas fa-comments"></i> المحادثات</h2>
                    </div>
                    <div class="chat-list" id="chatList"></div>
                </div>

                <!-- صفحة السوق -->
                <div id="marketPage" class="page">
                    <div class="market-header">
                        <h2><i class="fas fa-store"></i> السوق</h2>
                        <p>تسوق من متاجر موثوقة - اشتراك شهري حسب الدولة</p>
                        <select class="country-select" id="countrySelect" onchange="MarketSystem.changeCountry()">
                            <!-- دول متطورة -->
                            <option value="JO">🇯🇴 الأردن</option>
                            <option value="TR">🇹🇷 تركيا</option>
                            <option value="QA">🇶🇦 قطر</option>
                            <option value="SA">🇸🇦 السعودية</option>
                            <option value="KW">🇰🇼 الكويت</option>
                            <option value="AE">🇦🇪 الإمارات</option>
                            <option value="US">🇺🇸 أمريكا</option>
                            <option value="GB">🇬🇧 بريطانيا</option>
                            <option value="DE">🇩🇪 ألمانيا</option>
                            <option value="FR">🇫🇷 فرنسا</option>
                            <option value="CA">🇨🇦 كندا</option>
                            <option value="AU">🇦🇺 أستراليا</option>
                            <option value="JP">🇯🇵 اليابان</option>
                            <option value="KR">🇰🇷 كوريا الجنوبية</option>
                            <!-- دول نامية -->
                            <option value="BH">🇧🇭 البحرين</option>
                            <option value="OM">🇴🇲 عمان</option>
                            <option value="EG">🇪🇬 مصر</option>
                            <option value="IQ">🇮🇶 العراق</option>
                            <option value="MA">🇲🇦 المغرب</option>
                            <option value="TN">🇹🇳 تونس</option>
                            <option value="LB">🇱🇧 لبنان</option>
                            <option value="PS">🇵🇸 فلسطين</option>
                            <option value="YE">🇾🇪 اليمن</option>
                            <option value="SD">🇸🇩 السودان</option>
                            <option value="LY">🇱🇾 ليبيا</option>
                            <option value="DZ">🇩🇿 الجزائر</option>
                            <option value="MR">🇲🇷 موريتانيا</option>
                            <option value="SO">🇸🇴 الصومال</option>
                            <option value="DJ">🇩🇯 جيبوتي</option>
                            <option value="KM">🇰🇲 جزر القمر</option>
                        </select>
                    </div>

                    <div class="market-tabs">
                        <button class="market-tab active" onclick="MarketSystem.switchTab('stores')">
                            <i class="fas fa-store-alt"></i>
                            <span>المتاجر</span>
                        </button>
                        <button class="market-tab" onclick="MarketSystem.switchTab('products')">
                            <i class="fas fa-box"></i>
                            <span>المنتجات</span>
                        </button>
                    </div>

                    <div id="storesSection" class="market-section active">
                        <div class="section-header">
                            <h3><i class="fas fa-star"></i> المتاجر المميزة</h3>
                            <button class="add-store-btn" onclick="MarketSystem.openStoreRegistration()">
                                <i class="fas fa-plus"></i> فتح متجر
                            </button>
                        </div>
                        <div class="stores-grid" id="storesGrid"></div>
                    </div>

                    <div id="productsSection" class="market-section">
                        <div class="section-header">
                            <h3><i class="fas fa-fire"></i> أحدث المنتجات</h3>
                        </div>
                        <div class="products-grid" id="productsGrid"></div>
                    </div>
                </div>

                <!-- صفحة تفاصيل المتجر -->
                <div id="storeDetailPage" class="page">
                    <div class="store-detail-header">
                        <button class="store-detail-back" onclick="Navigation.switchPage('market')"><i class="fas fa-arrow-right"></i></button>
                        <div class="store-detail-info">
                            <div class="store-detail-logo" id="storeDetailLogo">🇯🇴</div>
                            <div>
                                <h2 id="storeDetailName">متجر الأردن</h2>
                                <p id="storeDetailMeta"><i class="fas fa-box"></i> 45 منتج • <i class="fas fa-users"></i> 1.2k متابع</p>
                            </div>
                        </div>
                        <button class="manage-store-btn" id="manageStoreBtn" onclick="MarketSystem.openManageStore()" style="display: none;">
                            <i class="fas fa-cog"></i> إدارة المتجر
                        </button>
                    </div>
                    <div class="store-products-grid" id="storeProductsGrid"></div>
                </div>

                <!-- صفحة إدارة المتجر -->
                <div id="manageStorePage" class="page">
                    <div class="manage-header">
                        <button class="manage-back" onclick="Navigation.switchPage('storeDetail')"><i class="fas fa-arrow-right"></i></button>
                        <h2>إدارة المتجر</h2>
                    </div>
                    <div class="manage-stats">
                        <div class="manage-stat-card">
                            <span class="manage-stat-value" id="manageProductCount">45</span>
                            <span class="manage-stat-label">منتج</span>
                        </div>
                        <div class="manage-stat-card">
                            <span class="manage-stat-value" id="manageOrderCount">12</span>
                            <span class="manage-stat-label">طلبات</span>
                        </div>
                        <div class="manage-stat-card">
                            <span class="manage-stat-value" id="manageRevenue">$1,240</span>
                            <span class="manage-stat-label">إيرادات</span>
                        </div>
                    </div>
                    <div class="manage-actions">
                        <button class="manage-action-btn" onclick="MarketSystem.addProduct()">
                            <i class="fas fa-plus-circle"></i> إضافة منتج
                        </button>
                        <button class="manage-action-btn" onclick="MarketSystem.editStoreInfo()">
                            <i class="fas fa-edit"></i> تعديل معلومات المتجر
                        </button>
                    </div>
                    <div class="manage-products-list" id="manageProductsList"></div>
                </div>

                <!-- صفحة الملف الشخصي (للمستخدم الحالي) -->
                <div id="profilePage" class="page">
                    <div class="profile-cover">
                        <div class="profile-avatar-container">
                            <div class="profile-avatar-large" id="profileAvatar">م</div>
                            <button class="edit-avatar-btn" onclick="ProfileSystem.editAvatar()">
                                <i class="fas fa-camera"></i>
                            </button>
                        </div>
                    </div>
                    
                    <div class="profile-info-header">
                        <div class="profile-name-wrapper">
                            <h1 class="profile-name" id="profileName">محمد أحمد</h1>
                            <span class="verified-badge"><i class="fas fa-check-circle"></i></span>
                            <button class="edit-name-btn" onclick="ProfileSystem.editName()">
                                <i class="fas fa-pen"></i>
                            </button>
                        </div>
                    </div>
                    
                    <div class="profile-username" id="profileUsername"><i class="fas fa-at"></i> mohammed</div>
                    
                    <div class="profile-bio" id="profileBio">
                        <i class="fas fa-info-circle"></i> مرحباً بك في Novalex
                        <button class="edit-bio-btn" onclick="ProfileSystem.editBio()">
                            <i class="fas fa-pen"></i>
                        </button>
                    </div>

                    <div class="account-status">
                        <span class="status-label"><i class="fas fa-lock"></i> حالة الحساب:</span>
                        <span class="status-badge public" id="accountStatus"><i class="fas fa-globe"></i> عام</span>
                        <button class="edit-status-btn" onclick="ProfileSystem.togglePrivacy()">
                            <i class="fas fa-pen"></i>
                        </button>
                    </div>

                    <div class="profile-stats">
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="profilePostsCount">42</div>
                            <div class="profile-stat-label">منشورات</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="profileFollowersCount">1.3ألف</div>
                            <div class="profile-stat-label">متابعين</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="profileFollowingCount">340</div>
                            <div class="profile-stat-label">متابَع</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="profileLikesCount">8.8ألف</div>
                            <div class="profile-stat-label">إعجابات</div>
                        </div>
                    </div>

                    <!-- العدادات المتطورة -->
                    <div class="advanced-stats-section">
                        <div class="advanced-stats-header" onclick="this.classList.toggle('active')">
                            <h3><i class="fas fa-chart-pie"></i> العدادات المتقدمة</h3>
                            <i class="fas fa-chevron-down toggle-icon"></i>
                        </div>
                        
                        <div class="advanced-stats-grid">
                            <div class="advanced-stat-card cs-stat-card" onclick="CustomerService.toggle()">
                                <div class="advanced-stat-icon"><i class="fas fa-headset"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">دعم 24/7</div>
                                    <div class="advanced-stat-label">خدمة العملاء</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card ai-stat-card" onclick="DeepSeekAI.toggle()">
                                <div class="advanced-stat-icon"><i class="fas fa-robot"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">DeepSeek</div>
                                    <div class="advanced-stat-label">مساعد ذكي</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="Settings.openFullSettings()">
                                <div class="advanced-stat-icon"><i class="fas fa-sliders-h"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">الإعدادات</div>
                                    <div class="advanced-stat-label">تخصيص الحساب</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="Settings.openPrivacy()">
                                <div class="advanced-stat-icon"><i class="fas fa-shield-alt"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">الخصوصية</div>
                                    <div class="advanced-stat-label">أمان الحساب</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="ProfileSystem.showSaved()">
                                <div class="advanced-stat-icon"><i class="fas fa-bookmark"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">24</div>
                                    <div class="advanced-stat-label">محفوظات</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="NotificationSystem.show()">
                                <div class="advanced-stat-icon"><i class="fas fa-bell"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">3</div>
                                    <div class="advanced-stat-label">إشعارات</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="Settings.changeLanguage()">
                                <div class="advanced-stat-icon"><i class="fas fa-language"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">العربية</div>
                                    <div class="advanced-stat-label">اللغة</div>
                                </div>
                            </div>
                            <div class="advanced-stat-card" onclick="Settings.toggleDarkMode()">
                                <div class="advanced-stat-icon"><i class="fas fa-moon"></i></div>
                                <div class="advanced-stat-content">
                                    <div class="advanced-stat-value">مظلم</div>
                                    <div class="advanced-stat-label">المظهر</div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="action-buttons">
                        <button class="action-btn primary" id="followBtn"><i class="fas fa-user-plus"></i> متابعة</button>
                        <button class="action-btn secondary" id="messageBtn" onclick="MessagesSystem.openChatWithUser(AuthSystem.currentUser.id)"><i class="fas fa-envelope"></i> رسالة</button>
                        <button class="action-btn danger" id="blockBtn" onclick="BlockSystem.blockUserFromProfile()"><i class="fas fa-ban"></i> حظر</button>
                    </div>

                    <div class="profile-tabs">
                        <div class="profile-tab active" onclick="ProfileSystem.switchTab('posts')">
                            <i class="fas fa-th"></i>
                            <span>المنشورات</span>
                        </div>
                        <div class="profile-tab" onclick="ProfileSystem.switchTab('reels')">
                            <i class="fas fa-film"></i>
                            <span>الريلز</span>
                        </div>
                        <div class="profile-tab" onclick="ProfileSystem.switchTab('tagged')">
                            <i class="fas fa-tag"></i>
                            <span>الوسوم</span>
                        </div>
                    </div>

                    <div class="profile-posts-grid" id="profilePostsGrid"></div>
                </div>

                <!-- صفحة ملف شخص آخر -->
                <div id="userProfilePage" class="page">
                    <div class="user-profile-header">
                        <button class="user-profile-back" onclick="Navigation.goBack()"><i class="fas fa-arrow-right"></i></button>
                        <div class="user-profile-info">
                            <div class="user-profile-avatar" id="userProfileAvatar">👤</div>
                            <div class="user-profile-details">
                                <h2 id="userProfileName">اسم المستخدم</h2>
                                <p id="userProfileUsername">@username</p>
                            </div>
                        </div>
                        <div style="display: flex; gap: 5px;">
                            <button class="message-user-btn" id="messageUserBtn" onclick="MessagesSystem.openChatWithUser(currentViewingUserId)">
                                <i class="fas fa-envelope"></i>
                            </button>
                            <!-- أيقونة ثلاث نقاط للقائمة -->
                            <button class="user-profile-menu-btn" onclick="UserProfileMenu.toggle(this, currentViewingUserId)">
                                <i class="fas fa-ellipsis-v"></i>
                            </button>
                        </div>
                    </div>
                    <!-- إحصائيات المستخدم الآخر -->
                    <div class="profile-stats">
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="userProfilePostsCount">0</div>
                            <div class="profile-stat-label">منشورات</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="userProfileFollowersCount">0</div>
                            <div class="profile-stat-label">متابعين</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="userProfileFollowingCount">0</div>
                            <div class="profile-stat-label">متابَع</div>
                        </div>
                        <div class="profile-stat">
                            <div class="profile-stat-value" id="userProfileLikesCount">0</div>
                            <div class="profile-stat-label">إعجابات</div>
                        </div>
                    </div>
                    <!-- منشورات المستخدم الآخر -->
                    <div class="user-profile-posts" id="userProfilePosts" style="padding:15px;">
                        <p style="text-align:center; color:var(--dark-500);">لا توجد منشورات بعد</p>
                    </div>
                </div>

                <!-- صفحة الإعدادات (معدلة) -->
                <div id="settingsPage" class="page">
                    <div class="settings-header">
                        <button class="settings-back" onclick="Navigation.switchPage('profile')"><i class="fas fa-arrow-right"></i></button>
                        <h2>الإعدادات</h2>
                    </div>
                    <div class="settings-section">
                        <h3 class="settings-section-title">المظهر</h3>
                        <div class="settings-item">
                            <div class="settings-item-icon"><i class="fas fa-moon"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">الوضع المظلم</div>
                                <div class="settings-item-description">تفعيل الوضع المظلم للتطبيق</div>
                            </div>
                            <label class="switch"><input type="checkbox" id="darkModeToggle" onchange="Settings.toggleDarkMode()"><span class="slider round"></span></label>
                        </div>
                    </div>
                    <div class="settings-section">
                        <h3 class="settings-section-title">الإشعارات</h3>
                        <div class="settings-item">
                            <div class="settings-item-icon"><i class="fas fa-bell"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">إشعارات التطبيق</div>
                                <div class="settings-item-description">استلام الإشعارات</div>
                            </div>
                            <label class="switch"><input type="checkbox" id="notificationsToggle" checked><span class="slider round"></span></label>
                        </div>
                    </div>
                    <div class="settings-section">
                        <h3 class="settings-section-title">اللغة</h3>
                        <div class="settings-item">
                            <div class="settings-item-icon"><i class="fas fa-language"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">لغة التطبيق</div>
                                <div class="settings-item-description">اختر لغتك المفضلة</div>
                            </div>
                            <select class="settings-select" id="languageSelect">
                                <option value="ar" selected>العربية</option>
                                <option value="en">English</option>
                            </select>
                        </div>
                    </div>
                    <!-- قسم المحفظة (جديد) -->
                    <div class="settings-section">
                        <h3 class="settings-section-title">المحفظة</h3>
                        <div class="settings-item" onclick="WalletSystem.showWallet()" style="cursor: pointer;">
                            <div class="settings-item-icon"><i class="fas fa-wallet"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">رصيدي: <span id="walletBalance">0</span> نقطة</div>
                                <div class="settings-item-description">اضغط لشحن الرصيد أو عرض السجل</div>
                            </div>
                            <i class="fas fa-chevron-left"></i>
                        </div>
                    </div>
                    <!-- قسم الإدارة (يظهر فقط للمشرفين) -->
                    <div class="settings-section" id="adminSection" style="display: none;">
                        <h3 class="settings-section-title">الإدارة</h3>
                        <div class="settings-item" onclick="Navigation.switchPage('adminReports')" style="cursor: pointer;">
                            <div class="settings-item-icon"><i class="fas fa-flag"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">البلاغات</div>
                                <div class="settings-item-description">عرض وإدارة البلاغات المقدمة</div>
                            </div>
                            <i class="fas fa-chevron-left"></i>
                        </div>
                    </div>
                    <!-- زر تسجيل الخروج ضمن الإعدادات -->
                    <div class="settings-section">
                        <h3 class="settings-section-title">الحساب</h3>
                        <div class="settings-item" onclick="AuthSystem.logout()" style="cursor: pointer;">
                            <div class="settings-item-icon"><i class="fas fa-sign-out-alt"></i></div>
                            <div class="settings-item-content">
                                <div class="settings-item-title">تسجيل الخروج</div>
                                <div class="settings-item-description">الخروج من حسابك الحالي</div>
                            </div>
                            <i class="fas fa-chevron-left"></i>
                        </div>
                    </div>
                </div>

                <!-- صفحة المحفظة (جديدة) -->
                <div id="walletPage" class="page">
                    <div class="settings-header" style="display: flex; align-items: center; padding: 15px; border-bottom: 1px solid var(--dark-200);">
                        <button class="settings-back" onclick="Navigation.switchPage('settings')"><i class="fas fa-arrow-right"></i></button>
                        <h2 style="margin-right: 15px;">محفظتي</h2>
                    </div>
                    <div style="padding: 20px; text-align: center;">
                        <div style="font-size: 48px; color: var(--primary-600);"><i class="fas fa-coins"></i></div>
                        <h3 style="margin: 15px 0;">رصيدك الحالي</h3>
                        <div style="font-size: 36px; font-weight: 800; color: var(--primary-600);" id="walletPageBalance">0</div>
                        <p style="color: var(--dark-500); margin: 10px 0;">نقطة</p>
                    </div>
                    <div style="padding: 0 20px;">
                        <h4>شحن الرصيد</h4>
                        <div style="display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; margin: 15px 0;">
                            <div class="plan-option" onclick="WalletSystem.charge(50)" style="text-align: center; padding: 15px; border: 2px solid var(--dark-200); border-radius: var(--radius-lg); cursor: pointer;">
                                <div style="font-size: 24px;">50</div>
                                <div style="font-size: 12px;">نقطة</div>
                                <div style="font-size: 11px; color: var(--dark-500);">$5</div>
                            </div>
                            <div class="plan-option" onclick="WalletSystem.charge(100)" style="text-align: center; padding: 15px; border: 2px solid var(--dark-200); border-radius: var(--radius-lg); cursor: pointer;">
                                <div style="font-size: 24px;">100</div>
                                <div style="font-size: 12px;">نقطة</div>
                                <div style="font-size: 11px; color: var(--dark-500);">$9</div>
                            </div>
                            <div class="plan-option" onclick="WalletSystem.charge(200)" style="text-align: center; padding: 15px; border: 2px solid var(--dark-200); border-radius: var(--radius-lg); cursor: pointer;">
                                <div style="font-size: 24px;">200</div>
                                <div style="font-size: 12px;">نقطة</div>
                                <div style="font-size: 11px; color: var(--dark-500);">$17</div>
                            </div>
                        </div>
                        <button class="store-reg-btn" onclick="WalletSystem.chargeCustom()" style="width: 100%; margin-top: 10px;">شحن رصيد (محاكاة)</button>
                    </div>
                </div>

                <!-- صفحة إدارة البلاغات -->
                <div id="adminReportsPage" class="page">
                    <div class="settings-header" style="display: flex; align-items: center; padding: 15px; border-bottom: 1px solid var(--dark-200);">
                        <button class="settings-back" onclick="Navigation.switchPage('settings')"><i class="fas fa-arrow-right"></i></button>
                        <h2 style="margin-right: 15px;">إدارة البلاغات</h2>
                    </div>
                    <div class="reports-list" id="reportsList" style="padding: 15px;">
                        <!-- سيتم عرض البلاغات هنا -->
                        <p style="text-align: center; color: var(--dark-500);">جاري تحميل البلاغات...</p>
                    </div>
                </div>
            </main><!-- شريط التنقل السفلي -->
        <nav class="bottom-nav">
            <button class="nav-item active" onclick="Navigation.switchPage('home', this)">
                <i class="fas fa-home"></i>
                <span>الرئيسية</span>
            </button>
            <button class="nav-item" onclick="Navigation.switchPage('live', this)">
                <i class="fas fa-video"></i>
                <span>مباشر</span>
            </button>
            <button class="nav-item" onclick="Navigation.switchPage('search', this)">
                <i class="fas fa-search"></i>
                <span>بحث</span>
            </button>
            <button class="nav-item" onclick="Navigation.switchPage('market', this)">
                <i class="fas fa-store"></i>
                <span>السوق</span>
            </button>
            <button class="nav-item" onclick="Navigation.switchPage('profile', this)">
                <i class="fas fa-user"></i>
                <span>ملفي</span>
            </button>
            <button class="nav-item publish-btn" onclick="PostModal.open()">
                <i class="fas fa-plus-circle"></i>
                <span>نشر</span>
            </button>
        </nav>
    </div> <!-- إغلاق div.app -->
</div> <!-- إغلاق div.phone-frame -->

<!-- =============== النوافذ المنبثقة =============== -->

<!-- نافذة إنشاء المنشور -->
<div class="create-post-modal" id="createPostModal">
    <div class="post-modal-overlay" onclick="PostModal.close()"></div>
    <div class="post-modal-content">
        <div class="post-modal-header">
            <div class="post-modal-avatar" id="modalUserAvatar">م</div>
            <div class="post-modal-user-info">
                <div class="post-modal-username" id="modalUsername">محمد أحمد</div>
                <div class="post-modal-time"><i class="fas fa-clock"></i> الآن</div>
            </div>
            <button class="post-modal-close" onclick="PostModal.close()"><i class="fas fa-times"></i></button>
        </div>

        <textarea class="post-modal-input" id="modalPostContent" placeholder="ما الذي تفكر فيه؟ (280 حرف)" rows="4" maxlength="280" oninput="PostModal.updateCounter()"></textarea>

        <div class="post-modal-counter">
            <span class="counter-text"><i class="fas fa-text-height"></i> <span id="modalCharCount">0/280</span></span>
            <div class="counter-progress">
                <div class="counter-bar" id="modalCharBar" style="width: 0%;"></div>
            </div>
        </div>

        <div class="post-media-tools">
            <button class="media-tool-btn" onclick="PostModal.addImage()">
                <i class="fas fa-image"></i>
                <span>صورة</span>
            </button>
            <button class="media-tool-btn" onclick="PostModal.addVideo()">
                <i class="fas fa-video"></i>
                <span>فيديو</span>
            </button>
        </div>

        <!-- منطقة تحرير الفيديو -->
        <div class="video-editor-area" id="videoEditorArea" style="display: none;">
            <div class="video-preview-container">
                <video id="videoPreview" controls></video>
                <div id="videoOverlay" class="video-overlay-layer"></div>
            </div>
            <div class="video-tools-vertical">
                <button class="tool-btn" onclick="VideoEditor.addText()" title="إضافة نص">
                    <i class="fas fa-font"></i>
                    <span>نص</span>
                </button>
                <button class="tool-btn" onclick="VideoEditor.addSticker()" title="إضافة ملصق">
                    <i class="fas fa-smile"></i>
                    <span>ملصقات</span>
                </button>
                <button class="tool-btn" onclick="VideoEditor.addEffect()" title="إضافة مؤثر">
                    <i class="fas fa-magic"></i>
                    <span>مؤثرات</span>
                </button>
                <button class="tool-btn" onclick="VideoEditor.addFilter()" title="إضافة فلتر">
                    <i class="fas fa-filter"></i>
                    <span>فلاتر</span>
                </button>
                <button class="tool-btn" onclick="VideoEditor.cropVideo()" title="قص الفيديو">
                    <i class="fas fa-crop-alt"></i>
                    <span>قص</span>
                </button>
            </div>
        </div>

        <!-- معاينة الوسائط -->
        <div class="post-media-preview" id="modalMediaPreview"></div>

        <div class="post-modal-location">
            <button class="location-toggle" onclick="PostModal.toggleLocation()">
                <i class="fas fa-map-marker-alt"></i> <span id="modalLocationText">إضافة موقع</span>
            </button>
            <div class="location-selector" id="modalLocationSelector">
                <div class="location-item" onclick="PostModal.selectLocation('عمان, الأردن')">
                    <i class="fas fa-map-pin"></i> عمان, الأردن
                </div>
                <div class="location-item" onclick="PostModal.selectLocation('دبي, الإمارات')">
                    <i class="fas fa-map-pin"></i> دبي, الإمارات
                </div>
                <div class="location-item" onclick="PostModal.selectLocation('الرياض, السعودية')">
                    <i class="fas fa-map-pin"></i> الرياض, السعودية
                </div>
                <div class="location-item" onclick="PostModal.selectLocation('القاهرة, مصر')">
                    <i class="fas fa-map-pin"></i> القاهرة, مصر
                </div>
            </div>
        </div>

        <!-- خيار إيقاف التعليقات -->
        <div class="post-options">
            <label class="option-checkbox">
                <input type="checkbox" id="disableCommentsCheckbox">
                <i class="fas fa-comment-slash"></i> إيقاف التعليقات على هذا المنشور
            </label>
        </div>

        <div class="promote-post-option" onclick="PromoSystem.showForPost()">
            <div class="promote-header">
                <i class="fas fa-rocket"></i>
                <h4>ترويج المنشور</h4>
            </div>
            <p style="font-size:13px; color:var(--dark-600);">اختر خطتك حسب الدولة (مشاهدات، تفاعل، رسائل)</p>
        </div>

        <div class="post-modal-actions">
            <button class="post-modal-publish" onclick="PostModal.publish()" id="publishBtn" disabled>
                <i class="fas fa-paper-plane"></i> نشر
            </button>
            <button class="post-modal-draft" onclick="PostModal.saveDraft()">
                <i class="fas fa-save"></i> مسودة
            </button>
        </div>
    </div>
</div>

<!-- نافذة التعليقات (مع دعم الردود) -->
<div class="comments-modal" id="commentsModal">
    <div class="comments-overlay" onclick="CommentsSystem.close()"></div>
    <div class="comments-content">
        <div class="comments-header">
            <h3><i class="fas fa-comments"></i> التعليقات</h3>
            <button class="comments-close" onclick="CommentsSystem.close()"><i class="fas fa-times"></i></button>
        </div>
        <div class="comments-list" id="commentsList"></div>
        <div class="comment-input-area">
            <input type="text" class="comment-input" id="commentInput" placeholder="أضف تعليقاً...">
            <button class="comment-send" onclick="CommentsSystem.addComment()"><i class="fas fa-paper-plane"></i></button>
        </div>
    </div>
</div>

<!-- نافذة المحادثة الخاصة -->
<div class="chat-window-modal" id="chatWindowModal" style="display:none; position:fixed; top:0; left:0; right:0; bottom:0; z-index:9500; background:var(--surface-light); flex-direction:column;">
    <div class="chat-window-header" style="display:flex; align-items:center; padding:15px; border-bottom:1px solid var(--dark-200); background:linear-gradient(135deg, var(--primary-600), var(--secondary-600)); color:white;">
        <button onclick="MessagesSystem.closeChatWindow()" style="background:none; border:none; color:white; font-size:20px; margin-left:10px;"><i class="fas fa-arrow-right"></i></button>
        <div class="chat-window-user" style="display:flex; align-items:center; gap:10px; flex:1;">
            <div class="chat-window-avatar" style="width:40px; height:40px; border-radius:50%; background:rgba(255,255,255,0.2); display:flex; align-items:center; justify-content:center; font-size:20px;">👤</div>
            <div>
                <h3 id="chatWindowUserName" style="font-size:16px;">اسم المستخدم</h3>
                <span id="chatWindowUserStatus" style="font-size:12px; opacity:0.8;">متصل</span>
            </div>
        </div>
        <!-- أزرار إضافية: مكالمة صوتية وإرفاق وسائط -->
        <button onclick="MessagesSystem.startVoiceCall()" title="مكالمة صوتية" style="background:none; border:none; color:white; font-size:20px; margin:0 5px;"><i class="fas fa-phone"></i></button>
        <button onclick="MessagesSystem.attachMedia()" title="إرفاق صورة أو فيديو" style="background:none; border:none; color:white; font-size:20px; margin:0 5px;"><i class="fas fa-paperclip"></i></button>
        <button onclick="MessagesSystem.closeChatWindow()" style="background:none; border:none; color:white; font-size:20px; margin-right:5px;"><i class="fas fa-times"></i></button>
    </div>
    <div class="chat-window-messages" id="chatWindowMessages" style="flex:1; overflow-y:auto; padding:15px; display:flex; flex-direction:column;"></div>
    <div class="chat-window-input" style="display:flex; padding:10px; border-top:1px solid var(--dark-200); background:var(--surface-light);">
        <input type="text" id="chatWindowInput" placeholder="اكتب رسالة..." style="flex:1; padding:12px; border:1px solid var(--dark-200); border-radius:var(--radius-full); margin-left:10px; font-size:14px;">
        <button onclick="MessagesSystem.sendMessage()" style="width:44px; height:44px; border-radius:50%; background:var(--primary-600); color:white; border:none;"><i class="fas fa-paper-plane"></i></button>
    </div>
</div>

<!-- إشعار المكالمة -->
<div class="call-notification" id="callNotification" style="position:fixed; top:50%; left:50%; transform:translate(-50%,-50%); background:var(--surface-light); padding:20px; border-radius:var(--radius-lg); box-shadow:var(--shadow-2xl); z-index:10000; text-align:center; display:none;">
    <i class="fas fa-phone-alt" style="font-size: 48px; color: var(--primary-600); margin-bottom: 15px;"></i>
    <h3>مكالمة صوتية</h3>
    <p>جاري الاتصال بـ <span id="callUserName">المستخدم</span>...</p>
    <button onclick="document.getElementById('callNotification').style.display='none'" style="padding: 10px 20px; background: var(--danger-500); color: white; border: none; border-radius: var(--radius-full); cursor: pointer;">إنهاء</button>
</div>

<!-- قائمة سياق خاصة بملف المستخدم -->
<div class="post-context-menu" id="userProfileContextMenu" style="display: none;">
    <ul>
        <li onclick="ReportSystem.reportUser()"><i class="fas fa-flag"></i> الإبلاغ عن هذا المستخدم</li>
        <li onclick="BlockSystem.blockUserFromProfile()"><i class="fas fa-ban"></i> حظر هذا المستخدم</li>
    </ul>
</div>

<!-- نافذة العرض الكامل للفيديو (ريلز) -->
<div class="reels-fullscreen" id="reelsFullscreen">
    <button class="reels-close" onclick="ReelsSystem.closeFullscreen()"><i class="fas fa-times"></i></button>
    <div class="reels-container" id="fullscreenReelsContainer"></div>
</div>

<!-- نافذة تسجيل المتجر -->
<div class="store-reg-modal" id="storeRegModal">
    <div class="store-reg-overlay" onclick="MarketSystem.closeStoreRegistration()"></div>
    <div class="store-reg-content">
        <button class="store-reg-close" onclick="MarketSystem.closeStoreRegistration()"><i class="fas fa-times"></i></button>
        <div class="store-reg-header">
            <h2><i class="fas fa-store"></i> فتح متجر جديد</h2>
            <p style="font-size: 13px; color: var(--dark-600); margin-top: 5px;">اختر الدولة أولاً ليظهر سعر الاشتراك</p>
        </div>
        <div class="store-form-group">
            <label>اسم المتجر</label>
            <input type="text" id="storeName" placeholder="أدخل اسم المتجر">
        </div>
        <div class="store-form-group">
            <label>الفئة</label>
            <select id="storeCategory">
                <option value="electronics">إلكترونيات</option>
                <option value="fashion">أزياء</option>
                <option value="beauty">جمال</option>
                <option value="home">منزل</option>
            </select>
        </div>
        <div class="location-request">
            <h4><i class="fas fa-map-marker-alt"></i> تحديد الموقع (اختياري)</h4>
            <div class="location-options">
                <button class="location-option-btn location-auto" onclick="MarketSystem.requestLocation()">
                    <i class="fas fa-crosshairs"></i> تحديد تلقائي
                </button>
                <button class="location-option-btn" onclick="MarketSystem.manualLocation()">
                    <i class="fas fa-pen"></i> إدخال يدوي
                </button>
            </div>
            <div class="location-status" id="locationStatus">
                <i class="fas fa-check-circle"></i> <span>تم تحديد موقعك</span>
            </div>
        </div>
        <div class="subscription-plans">
            <h4><i class="fas fa-crown"></i> اختر خطة الاشتراك</h4>
            <p style="font-size: 12px; color: var(--info-600); margin-bottom: 10px;">
                <i class="fas fa-globe"></i> السعر يعتمد على الدولة المحددة: <span id="selectedCountryName">الأردن</span>
            </p>
            <p style="font-size: 12px; color: var(--warning-600); margin-bottom: 10px;">
                <i class="fas fa-info-circle"></i> اشتراك شهري - 30 يوماً
            </p>
            <div class="plans-grid">
                <div class="plan-option selected" onclick="MarketSystem.selectPlan('basic')">
                    <div class="plan-name">Basic</div>
                    <div class="plan-price" id="storePlanPrice"><!-- السعر يظهر بعد اختيار الدولة --></div>
                </div>
                <div class="plan-option" onclick="MarketSystem.selectPlan('pro')">
                    <div class="plan-name">Pro</div>
                    <div class="plan-price" id="storePlanPricePro"><!-- السعر يظهر بعد اختيار الدولة --></div>
                </div>
                <div class="plan-option" onclick="MarketSystem.selectPlan('premium')">
                    <div class="plan-name">Premium</div>
                    <div class="plan-price" id="storePlanPricePremium"><!-- السعر يظهر بعد اختيار الدولة --></div>
                </div>
            </div>
        </div>
        <button class="store-reg-btn" onclick="MarketSystem.completeRegistration()">
            <i class="fas fa-check"></i> تأكيد وفتح المتجر
        </button>
    </div>
</div>

<!-- نافذة الترويج -->
<div class="promo-modal" id="promoModal">
    <div class="promo-overlay" onclick="PromoSystem.close()"></div>
    <div class="promo-content">
        <button class="promo-close" onclick="PromoSystem.close()"><i class="fas fa-times"></i></button>
        <div class="promo-header">
            <h2><i class="fas fa-rocket"></i> ترويج المنشور</h2>
            <p id="promoCountryHint">السعر يبدأ من 2$ لليوم حسب الدولة</p>
        </div>
        
        <div class="promo-days">
            <label>عدد الأيام:</label>
            <input type="number" id="promoDays" min="1" max="30" value="1" onchange="PromoSystem.calculateTotal()">
        </div>
        
        <div class="promo-price-input">
            <label>السعر لليوم ($):</label>
            <input type="number" id="promoPricePerDay" min="2" step="0.5" value="2" onchange="PromoSystem.calculateTotal()">
            <small class="price-hint">الحد الأدنى 2$ للدول النامية، 5$ للمتطورة</small>
        </div>
        
        <div class="promo-goals">
            <div class="goal-item active" onclick="PromoSystem.selectGoal('views')">
                <i class="fas fa-eye"></i>
                <span>مشاهدات</span>
            </div>
            <div class="goal-item" onclick="PromoSystem.selectGoal('engagement')">
                <i class="fas fa-heart"></i>
                <span>تفاعل</span>
            </div>
            <div class="goal-item" onclick="PromoSystem.selectGoal('messages')">
                <i class="fas fa-envelope"></i>
                <span>رسائل</span>
            </div>
        </div>
        
        <div class="promo-total">
            <span>الإجمالي</span>
            <span id="promoTotal">$2</span>
        </div>
        <button class="promo-confirm-btn" onclick="PromoSystem.confirm()">
            <i class="fas fa-bolt"></i> تأكيد الترويج
        </button>
    </div>
</div>

<!-- نافذة البث المباشر (محدّثة – مع الهدايا ورصيد المحفظة) -->
<div class="live-stream-modal" id="liveStreamModal">
    <div class="live-stream-video">
        <video id="liveVideo" autoplay muted></video>
        <div class="live-stream-overlay">
            <div class="live-stream-info">
                <div class="live-stream-avatar" id="liveStreamAvatar">م</div>
                <div class="live-stream-details">
                    <h3 id="liveStreamName">محمد أحمد</h3>
                    <p><i class="fas fa-circle"></i> 1.2k مشاهد</p>
                </div>
            </div>
            <button class="live-stream-close" onclick="LiveSystem.closeStream()"><i class="fas fa-times"></i></button>
        </div>
        <button class="gifts-toggle-btn" onclick="LiveSystem.toggleGifts()">
            <i class="fas fa-gift"></i> إرسال هدية
        </button>
        <div class="live-gifts-section" id="liveGiftsSection">
            <div class="gifts-header">
                <h4><i class="fas fa-gift"></i> الهدايا</h4>
                <button class="gifts-close" onclick="LiveSystem.toggleGifts()"><i class="fas fa-times"></i></button>
            </div>
            <div class="gifts-grid" id="giftsGrid">
                <!-- سيتم إنشاء الهدايا عبر JavaScript -->
            </div>
            <div style="padding: 10px; border-top: 1px solid var(--dark-200); display: flex; justify-content: space-between; align-items: center;">
                <span><i class="fas fa-coins"></i> رصيدك: <span id="liveWalletBalance">0</span></span>
                <a href="#" onclick="Navigation.switchPage('wallet'); LiveSystem.closeStream();" style="color: var(--primary-600);">شحن</a>
            </div>
        </div>
    </div>
</div>

<!-- مركز الإشعارات -->
<div class="notification-center" id="notificationCenter">
    <div class="notification-header">
        <button class="notification-back-btn" onclick="NotificationSystem.goBack()"><i class="fas fa-arrow-right"></i></button>
        <h3><i class="fas fa-bell"></i> الإشعارات</h3>
        <button class="notification-mark-read" onclick="NotificationSystem.markAllRead()"><i class="fas fa-check-double"></i></button>
    </div>
    <div class="notification-list" id="notificationList"></div>
</div>

<!-- نافذة خدمة العملاء -->
<div class="cs-chat" id="csChat" style="display: none; position: fixed; bottom: 100px; right: 20px; width: 300px; height: 400px; background: var(--surface-light); border-radius: var(--radius-2xl); box-shadow: var(--shadow-2xl); z-index: 10000; flex-direction: column; overflow: hidden;">
    <div class="cs-header" style="padding: 15px; background: linear-gradient(135deg, var(--primary-600), var(--secondary-600)); color: white; display: flex; justify-content: space-between;">
        <h4>خدمة العملاء</h4>
        <button onclick="CustomerService.hide()"><i class="fas fa-times"></i></button>
    </div>
    <div class="cs-messages" id="csMessages" style="flex: 1; padding: 15px; overflow-y: auto;"></div>
    <div class="cs-input-area" style="display: flex; padding: 10px; gap: 10px; border-top: 1px solid var(--dark-200);">
        <input type="text" class="cs-input" id="csInput" placeholder="اكتب مشكلتك..." style="flex: 1; padding: 10px; border: 1px solid var(--dark-200); border-radius: var(--radius-full);">
        <button class="cs-send" onclick="CustomerService.sendMessage()" style="width: 40px; height: 40px; border-radius: 50%; background: var(--primary-600); color: white; border: none;"><i class="fas fa-paper-plane"></i></button>
    </div>
</div>

<!-- نافذة DeepSeek AI -->
<div class="ai-chat" id="aiChat" style="display: none; position: fixed; bottom: 100px; left: 20px; width: 300px; height: 400px; background: var(--surface-light); border-radius: var(--radius-2xl); box-shadow: var(--shadow-2xl); z-index: 10000; flex-direction: column; overflow: hidden;">
    <div class="ai-header" style="padding: 15px; background: #10a37f; color: white; display: flex; justify-content: space-between;">
        <h4>DeepSeek AI</h4>
        <button onclick="DeepSeekAI.hide()"><i class="fas fa-times"></i></button>
    </div>
    <div class="ai-messages" id="aiMessages" style="flex: 1; padding: 15px; overflow-y: auto;"></div>
    <div class="ai-input-area" style="display: flex; padding: 10px; gap: 10px; border-top: 1px solid var(--dark-200);">
        <input type="text" class="ai-input" id="aiInput" placeholder="اسألني..." style="flex: 1; padding: 10px; border: 1px solid var(--dark-200); border-radius: var(--radius-full);">
        <button class="ai-send" onclick="DeepSeekAI.sendMessage()" style="width: 40px; height: 40px; border-radius: 50%; background: #10a37f; color: white; border: none;"><i class="fas fa-paper-plane"></i></button>
    </div>
</div>

<!-- قائمة سياق المنشور (للإبلاغ والحظر) -->
<div class="post-context-menu" id="postContextMenu" style="display: none;">
    <ul>
        <li onclick="ReportSystem.reportPost()"><i class="fas fa-flag"></i> الإبلاغ عن هذا المنشور</li>
        <li onclick="BlockSystem.blockUser()"><i class="fas fa-ban"></i> حظر هذا المستخدم</li>
    </ul>
</div><!-- =============== القسم 1: الإعدادات الأساسية (مع إضافة WalletSystem) =============== -->
    <script>
        const IDGenerator = {
            generate: (prefix = 'id') => prefix + '_' + Date.now() + '_' + Math.random().toString(36).substr(2, 9),
            setUniqueId: (element) => {
                if (element && !element.hasAttribute('data-unique-id')) {
                    element.setAttribute('data-unique-id', IDGenerator.generate('elem'));
                }
                return element ? element.getAttribute('data-unique-id') : null;
            }
        };

        const UI = {
            showToast: (message, type = 'info') => {
                const toast = document.createElement('div');
                toast.className = `toast ${type}`;
                toast.innerHTML = `<i class="fas ${type === 'success' ? 'fa-check-circle' : type === 'error' ? 'fa-exclamation-circle' : 'fa-info-circle'}"></i> ${message}`;
                document.body.appendChild(toast);
                setTimeout(() => toast.remove(), 3000);
            }
        };

        const UsersManager = {
            users: [],
            init: function() {
                this.users = [
                    { id: 'user_1', name: 'محمد أحمد', username: 'mohammed', avatar: 'م', bio: 'مرحباً بك في Novalex', isPrivate: false, followersCount: 1300, followingCount: 340, postsCount: 42, likesCount: 8800 },
                    { id: 'user_2', name: 'سارة علي', username: 'sara', avatar: 'س', bio: 'مصممة جرافيك', isPrivate: false, followersCount: 2300, followingCount: 180, postsCount: 67, likesCount: 15400 },
                    { id: 'user_3', name: 'أحمد خالد', username: 'ahmed', avatar: 'أ', bio: 'مبرمج ومطور ويب', isPrivate: true, followersCount: 890, followingCount: 120, postsCount: 23, likesCount: 4200 }
                ];
            },
            getUserById: function(id) { return this.users.find(u => u.id === id) || null; },
            getUserByUsername: function(username) { return this.users.find(u => u.username === username) || null; },
            getAllUsers: function() { return this.users; }
        };

        // نظام المحفظة (Wallet)
        const WalletSystem = {
            balance: 0,
            init: function() {
                const saved = localStorage.getItem('novalex_wallet');
                this.balance = saved ? parseInt(saved) : 100; // رصيد ابتدائي 100 للتجربة
                this.updateUI();
            },
            updateUI: function() {
                document.getElementById('walletBalance').textContent = this.balance;
                document.getElementById('walletPageBalance').textContent = this.balance;
                const liveBalance = document.getElementById('liveWalletBalance');
                if (liveBalance) liveBalance.textContent = this.balance;
            },
            showWallet: function() {
                Navigation.switchPage('wallet');
                this.updateUI();
            },
            charge: function(amount) {
                this.balance += amount;
                localStorage.setItem('novalex_wallet', this.balance);
                this.updateUI();
                UI.showToast(`✅ تم شحن ${amount} نقطة`, 'success');
            },
            chargeCustom: function() {
                const amount = prompt('أدخل المبلغ (نقطة):', '100');
                if (amount && !isNaN(amount) && parseInt(amount) > 0) {
                    this.charge(parseInt(amount));
                }
            },
            deduct: function(amount, callback) {
                if (this.balance >= amount) {
                    this.balance -= amount;
                    localStorage.setItem('novalex_wallet', this.balance);
                    this.updateUI();
                    if (callback) callback(true);
                } else {
                    UI.showToast('❌ رصيد غير كافٍ', 'error');
                    if (callback) callback(false);
                }
            }
        };

        const AuthSystem = {
            currentUser: { id: 'user_1', name: 'محمد أحمد', username: 'mohammed', avatar: 'م', email: 'test@novalex.com', bio: 'مرحباً بك في Novalex', isPrivate: false },
            init: function() { UsersManager.init(); this.showLoginScreen(); },
            showLoginScreen: function() {
                document.getElementById('authScreen').classList.remove('hidden');
                document.getElementById('mainHeader').classList.add('hidden');
                document.getElementById('mainContent').classList.add('hidden');
            },
            login: function() {
                document.getElementById('authScreen').classList.add('hidden');
                document.getElementById('mainHeader').classList.remove('hidden');
                document.getElementById('mainContent').classList.remove('hidden');
                UI.showToast('✅ تم تسجيل الدخول', 'success');
                this.updateUI();
                if (Stories) Stories.loadStories();
                if (LiveSystem) LiveSystem.loadLiveStreams();
                if (MarketSystem) MarketSystem.init();
                if (FeedSystem) FeedSystem.loadFeed();
                if (ChatSystem) ChatSystem.init();
                if (typeof checkIfAdmin === 'function') checkIfAdmin();
                if (WalletSystem) WalletSystem.init(); // تهيئة المحفظة
            },
            showRegister: function() { UI.showToast('🔄 قريباً', 'info'); },
            updateUI: function() {
                document.querySelectorAll('.user-avatar, .profile-avatar-large, .post-modal-avatar').forEach(el => { if (el) el.textContent = this.currentUser.avatar; });
                document.getElementById('profileName').textContent = this.currentUser.name;
                document.getElementById('profileUsername').innerHTML = '<i class="fas fa-at"></i> ' + this.currentUser.username;
                document.getElementById('profileBio').innerHTML = '<i class="fas fa-info-circle"></i> ' + this.currentUser.bio + '<button class="edit-bio-btn" onclick="ProfileSystem.editBio()"><i class="fas fa-pen"></i></button>';
                const userData = UsersManager.getUserById(this.currentUser.id);
                if (userData) {
                    document.getElementById('profilePostsCount').textContent = userData.postsCount;
                    document.getElementById('profileFollowersCount').textContent = userData.followersCount > 1000 ? (userData.followersCount/1000).toFixed(1) + 'ألف' : userData.followersCount;
                    document.getElementById('profileFollowingCount').textContent = userData.followingCount;
                    document.getElementById('profileLikesCount').textContent = userData.likesCount > 1000 ? (userData.likesCount/1000).toFixed(1) + 'ألف' : userData.likesCount;
                }
            },
            logout: function() {
                document.getElementById('mainHeader').classList.add('hidden');
                document.getElementById('mainContent').classList.add('hidden');
                document.getElementById('authScreen').classList.remove('hidden');
                document.getElementById('notificationCenter')?.classList.remove('active');
                document.getElementById('createPostModal')?.classList.remove('active');
                document.getElementById('promoModal')?.classList.remove('active');
                document.getElementById('liveStreamModal')?.classList.remove('active');
                document.getElementById('commentsModal')?.classList.remove('active');
                document.querySelectorAll('.nav-item').forEach(item => item.classList.remove('active'));
                document.querySelector('.nav-item[onclick*="home"]')?.classList.add('active');
                UI.showToast('👋 تم تسجيل الخروج', 'info');
            }
        };

        const Navigation = {
            switchPage: function(page, clickedElement) {
                document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
                const target = document.getElementById(page + 'Page');
                if (target) target.classList.add('active-page');
                document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
                if (clickedElement) clickedElement.classList.add('active');
            },
            goBack: function() { this.switchPage('home'); },
            viewUserProfile: function(userId) {
                const user = UsersManager.getUserById(userId);
                if (!user) { UI.showToast('المستخدم غير موجود', 'error'); return; }
                document.getElementById('userProfileAvatar').textContent = user.avatar;
                document.getElementById('userProfileName').textContent = user.name;
                document.getElementById('userProfileUsername').textContent = '@' + user.username;
                document.getElementById('userProfilePostsCount').textContent = user.postsCount;
                document.getElementById('userProfileFollowersCount').textContent = user.followersCount > 1000 ? (user.followersCount/1000).toFixed(1) + 'ألف' : user.followersCount;
                document.getElementById('userProfileFollowingCount').textContent = user.followingCount;
                document.getElementById('userProfileLikesCount').textContent = user.likesCount > 1000 ? (user.likesCount/1000).toFixed(1) + 'ألف' : user.likesCount;
                window.currentViewingUserId = userId;
                this.switchPage('userProfile');
            }
        };
    </script>

    <!-- =============== القسم 2: أنظمة المحتوى (مع دعم الردود على التعليقات) =============== -->
    <script>
        const Stories = {
            stories: [
                { id: IDGenerator.generate('story'), user: 'قصتك', avatar: '+', isAdd: true, userId: null },
                { id: IDGenerator.generate('story'), user: 'محمد', avatar: 'م', userId: 'user_1' },
                { id: IDGenerator.generate('story'), user: 'سارة', avatar: 'س', userId: 'user_2' }
            ],
            loadStories: function() {
                const container = document.getElementById('storiesContainer');
                if (!container) return;
                container.innerHTML = '';
                this.stories.forEach(s => {
                    const el = document.createElement('div');
                    el.className = 'story-item';
                    el.setAttribute('data-story-id', s.id);
                    el.onclick = () => s.userId ? Navigation.viewUserProfile(s.userId) : UI.showToast(`📖 قصة ${s.user}`);
                    el.innerHTML = `<div class="story-ring"><div class="story-avatar ${s.isAdd ? 'story-add' : ''}">${s.avatar}</div></div><span class="story-name">${s.user}</span>`;
                    container.appendChild(el);
                });
            }
        };

        const PostSystem = {
            updateCounter: function(input) {
                const counter = document.getElementById('postCharCounter');
                const text = document.getElementById('postCharText');
                const bar = document.getElementById('postCharBar');
                if (!counter || !text || !bar) return;
                counter.style.display = 'flex';
                const count = input.value.length;
                text.textContent = `${count}/280`;
                const percent = (count / 280) * 100;
                bar.style.width = percent + '%';
                bar.classList.remove('warning', 'danger');
                if (percent > 90) bar.classList.add('danger');
                else if (percent > 75) bar.classList.add('warning');
            }
        };

        const FeedSystem = {
            posts: [
                { id: 1, userId: 'user_1', user: 'محمد أحمد', username: '@mohammed', avatar: 'م', type: 'text', content: 'مرحباً بكم في نوفالكس!', time: 'منذ ساعة', likes: 124, comments: 23, shares: 12, commentsDisabled: false },
                { id: 2, userId: 'user_2', user: 'سارة', username: '@sara', avatar: 'س', type: 'image', content: 'منظر غروب', image: 'https://picsum.photos/400/400?random=1', time: 'منذ 3 ساعات', likes: 345, comments: 45, shares: 23, commentsDisabled: false },
                { id: 3, userId: 'user_3', user: 'أحمد', username: '@ahmed', avatar: 'أ', type: 'video', content: 'فيديو جديد', video: 'https://www.w3schools.com/html/mov_bbb.mp4', time: 'منذ 5 ساعات', likes: 567, comments: 78, shares: 34, commentsDisabled: false }
            ],
            loadFeed: function() {
                const container = document.getElementById('mainFeed');
                if (!container) return;
                container.innerHTML = '';
                this.posts.forEach(post => {
                    const item = document.createElement('div');
                    item.className = 'feed-item';
                    let mediaHtml = '';
                    if (post.type === 'image') mediaHtml = `<div class="feed-media"><img src="${post.image}" onclick="FeedSystem.openPost(${post.id})"></div>`;
                    else if (post.type === 'video') mediaHtml = `<div class="feed-media"><video src="${post.video}" controls onclick="FeedSystem.openPost(${post.id})"></video></div>`;
                    
                    let commentsButton = '';
                    if (post.commentsDisabled) {
                        commentsButton = `<button class="feed-action disabled" title="التعليقات موقوفة"><i class="fas fa-comment-slash"></i> <span>${this.formatNumber(post.comments)}</span></button>`;
                    } else {
                        commentsButton = `<button class="feed-action" onclick="CommentsSystem.open(${post.id})"><i class="fas fa-comment"></i> <span>${this.formatNumber(post.comments)}</span></button>`;
                    }
                    
                    item.innerHTML = `
                        <div class="feed-header">
                            <div class="feed-avatar" onclick="Navigation.viewUserProfile('${post.userId}')" style="cursor:pointer;">${post.avatar}</div>
                            <div class="feed-user-info" onclick="Navigation.viewUserProfile('${post.userId}')" style="cursor:pointer;">
                                <div class="feed-username">${post.user}</div>
                                <div class="feed-time"><i class="fas fa-clock"></i> ${post.time}</div>
                            </div>
                            <div class="post-menu" onclick="PostMenu.toggle(this, ${post.id}, '${post.user}')">
                                <i class="fas fa-ellipsis-v"></i>
                            </div>
                        </div>
                        <div class="feed-content-text">${post.content}</div>
                        ${mediaHtml}
                        <div class="feed-actions">
                            <button class="feed-action" onclick="FeedSystem.like(this, ${post.id})"><i class="fas fa-heart"></i> <span>${this.formatNumber(post.likes)}</span></button>
                            ${commentsButton}
                            <button class="feed-action" onclick="FeedSystem.share(${post.id})"><i class="fas fa-share"></i> <span>${this.formatNumber(post.shares)}</span></button>
                            <button class="feed-promo-btn" onclick="PromoSystem.showForPost(${post.id})"><i class="fas fa-rocket"></i> ترويج</button>
                        </div>
                    `;
                    container.appendChild(item);
                });
            },
            like: function(btn, id) {
                btn.classList.toggle('liked');
                const span = btn.querySelector('span');
                let count = parseInt(span.textContent.replace(/[^0-9]/g, ''));
                if (isNaN(count)) count = 0;
                count = btn.classList.contains('liked') ? count + 1 : count - 1;
                span.textContent = this.formatNumber(count);
            },
            share: function(id) { UI.showToast('🔗 تم نسخ الرابط', 'info'); },
            openPost: function(id) { UI.showToast('📱 فتح المنشور', 'info'); },
            formatNumber: function(num) {
                if (num >= 1000000) return (num/1000000).toFixed(1) + 'م';
                if (num >= 1000) return (num/1000).toFixed(1) + 'ألف';
                return num;
            }
        };

        const PostModal = {
            selectedMedia: [],
            location: null,
            open: function(type = '') { 
                document.getElementById('createPostModal').classList.add('active');
                if (type === 'image') this.addImage();
                else if (type === 'video') this.addVideo();
            },
            openWithImage: function() { this.open('image'); },
            openWithVideo: function() { this.open('video'); },
            close: function() { 
                document.getElementById('createPostModal').classList.remove('active');
                VideoEditor.hideEditor();
            },
            updateCounter: function() {
                const input = document.getElementById('modalPostContent');
                const count = input.value.length;
                document.getElementById('modalCharCount').textContent = count + '/280';
                document.getElementById('modalCharBar').style.width = (count / 280 * 100) + '%';
                document.getElementById('publishBtn').disabled = count === 0 && this.selectedMedia.length === 0;
            },
            toggleLocation: function() {
                document.getElementById('modalLocationSelector').classList.toggle('active');
            },
            selectLocation: function(loc) {
                this.location = loc;
                document.getElementById('modalLocationText').textContent = loc;
                document.getElementById('modalLocationSelector').classList.remove('active');
            },
            addImage: function() {
                const input = document.createElement('input');
                input.type = 'file';
                input.accept = 'image/*';
                input.multiple = true;
                input.onchange = (e) => {
                    Array.from(e.target.files).forEach(file => {
                        const reader = new FileReader();
                        reader.onload = (e) => {
                            const preview = document.getElementById('modalMediaPreview');
                            const item = document.createElement('div');
                            item.className = 'preview-item';
                            item.innerHTML = `<img src="${e.target.result}" style="width:100%; height:100%; object-fit:cover;"><span class="preview-remove" onclick="this.parentElement.remove(); PostModal.selectedMedia = PostModal.selectedMedia.filter(m => m.data !== '${e.target.result}');">✕</span>`;
                            preview.appendChild(item);
                            this.selectedMedia.push({ type: 'image', data: e.target.result });
                            this.updateCounter();
                        };
                        reader.readAsDataURL(file);
                    });
                };
                input.click();
            },
            addVideo: function() {
                const input = document.createElement('input');
                input.type = 'file';
                input.accept = 'video/*';
                input.onchange = (e) => {
                    const file = e.target.files[0];
                    if (file) {
                        const reader = new FileReader();
                        reader.onload = (e) => {
                            const videoSrc = e.target.result;
                            const preview = document.getElementById('modalMediaPreview');
                            const item = document.createElement('div');
                            item.innerHTML = `<video src="${videoSrc}" style="width:100%; height:100%; object-fit:cover;"></video><span class="preview-remove" onclick="this.parentElement.remove(); PostModal.selectedMedia = PostModal.selectedMedia.filter(m => m.data !== '${videoSrc}'); VideoEditor.hideEditor();">✕</span>`;
                            preview.appendChild(item);
                            this.selectedMedia.push({ type: 'video', data: videoSrc });
                            VideoEditor.showEditor(videoSrc);
                            this.updateCounter();
                        };
                        reader.readAsDataURL(file);
                    }
                };
                input.click();
            },
            publish: function() {
                const content = document.getElementById('modalPostContent').value.trim();
                if (!content && this.selectedMedia.length === 0) {
                    UI.showToast('❌ أضف نصاً أو وسائط', 'error');
                    return;
                }
                const disableComments = document.getElementById('disableCommentsCheckbox').checked;
                const newPost = {
                    id: Date.now(),
                    userId: AuthSystem.currentUser.id,
                    user: AuthSystem.currentUser.name,
                    username: AuthSystem.currentUser.username,
                    avatar: AuthSystem.currentUser.avatar,
                    type: this.selectedMedia.length ? this.selectedMedia[0].type : 'text',
                    content: content,
                    media: this.selectedMedia,
                    location: this.location,
                    time: 'الآن',
                    likes: 0,
                    comments: 0,
                    shares: 0,
                    commentsDisabled: disableComments
                };
                FeedSystem.posts.unshift(newPost);
                FeedSystem.loadFeed();
                UI.showToast('✅ تم النشر' + (this.location ? ' في ' + this.location : ''), 'success');
                document.getElementById('modalPostContent').value = '';
                this.location = null;
                document.getElementById('modalLocationText').textContent = 'إضافة موقع';
                this.selectedMedia = [];
                document.getElementById('modalMediaPreview').innerHTML = '';
                document.getElementById('disableCommentsCheckbox').checked = false;
                this.updateCounter();
                this.close();
            },
            saveDraft: function() {
                UI.showToast('📝 تم حفظ المسودة', 'info');
                this.close();
            }
        };

        const VideoEditor = {
            overlay: document.getElementById('videoOverlay'),
            preview: document.getElementById('videoPreview'),
            showEditor: function(videoSrc) {
                const area = document.getElementById('videoEditorArea');
                if (!area) return;
                area.style.display = 'flex';
                if (this.preview) {
                    this.preview.src = videoSrc;
                    this.preview.load();
                    this.preview.play().catch(e => {});
                }
                if (this.overlay) this.overlay.innerHTML = '';
            },
            hideEditor: function() {
                const area = document.getElementById('videoEditorArea');
                if (area) area.style.display = 'none';
                if (this.preview) this.preview.pause();
            },
            addText: function() {
                const text = prompt('أدخل النص الذي تريد إضافته على الفيديو:');
                if (!text) return;
                const el = document.createElement('div');
                el.className = 'draggable-text';
                el.textContent = text;
                el.style.top = '50%'; el.style.left = '50%'; el.style.transform = 'translate(-50%,-50%)';
                this.makeDraggable(el);
                this.overlay.appendChild(el);
            },
            addSticker: function() {
                const stickers = ['😊','😂','❤️','🔥','👍','🎉','😎','🥺','😍','✨'];
                const sticker = stickers[Math.floor(Math.random() * stickers.length)];
                const el = document.createElement('div');
                el.className = 'draggable-text';
                el.style.fontSize = '48px';
                el.style.background = 'transparent';
                el.textContent = sticker;
                el.style.top = '50%'; el.style.left = '50%'; el.style.transform = 'translate(-50%,-50%)';
                this.makeDraggable(el);
                this.overlay.appendChild(el);
            },
            addEffect: function() {
                const effects = ['blur(2px)','brightness(1.5)','contrast(150%)','grayscale(100%)','sepia(100%)'];
                const effect = effects[Math.floor(Math.random() * effects.length)];
                this.preview.style.filter = effect;
                UI.showToast('✨ تم تطبيق التأثير', 'success');
            },
            addFilter: function() {
                const panel = document.createElement('div');
                panel.className = 'filter-panel active';
                panel.innerHTML = `
                    <h4>اختر فلتر</h4>
                    <div class="filter-option" onclick="VideoEditor.applyFilter('none')">عادي</div>
                    <div class="filter-option" onclick="VideoEditor.applyFilter('grayscale(100%)')">أبيض وأسود</div>
                    <div class="filter-option" onclick="VideoEditor.applyFilter('sepia(100%)')">قديم</div>
                    <div class="filter-option" onclick="VideoEditor.applyFilter('brightness(1.2) contrast(1.2)')">مشرق</div>
                    <div class="filter-option" onclick="VideoEditor.applyFilter('hue-rotate(90deg)')">ملون</div>
                    <button onclick="this.parentElement.remove()">إغلاق</button>
                `;
                document.body.appendChild(panel);
            },
            applyFilter: function(filterValue) {
                if (filterValue === 'none') this.preview.style.filter = '';
                else this.preview.style.filter = filterValue;
                document.querySelector('.filter-panel')?.remove();
                UI.showToast('🎨 تم تطبيق الفلتر', 'success');
            },
            cropVideo: function() {
                const start = prompt('بداية (ثانية):');
                const end = prompt('نهاية (ثانية):');
                if (start && end) UI.showToast(`سيتم قص الفيديو من ${start}ث إلى ${end}ث`, 'info');
            },
            makeDraggable: function(el) {
                let dragging = false, offsetX, offsetY;
                el.addEventListener('mousedown', e => {
                    dragging = true;
                    const rect = el.getBoundingClientRect();
                    offsetX = e.clientX - rect.left;
                    offsetY = e.clientY - rect.top;
                    el.style.cursor = 'grabbing';
                });
                document.addEventListener('mousemove', e => {
                    if (!dragging) return;
                    const containerRect = this.overlay.getBoundingClientRect();
                    let newX = e.clientX - offsetX - containerRect.left;
                    let newY = e.clientY - offsetY - containerRect.top;
                    newX = Math.max(0, Math.min(newX, containerRect.width - el.offsetWidth));
                    newY = Math.max(0, Math.min(newY, containerRect.height - el.offsetHeight));
                    el.style.left = newX + 'px';
                    el.style.top = newY + 'px';
                    el.style.transform = 'none';
                });
                document.addEventListener('mouseup', () => { dragging = false; el.style.cursor = 'move'; });
            }
        };

        // CommentsSystem مع دعم الردود
        const CommentsSystem = {
            comments: [
                { id: 1, postId: 1, parentId: null, user: 'أحمد', avatar: 'أ', text: 'فيديو رائع!', time: 'منذ 5 دقائق', likes: 12 },
                { id: 2, postId: 1, parentId: 1, user: 'سارة', avatar: 'س', text: 'شكراً أحمد', time: 'منذ 4 دقائق', likes: 5 },
                { id: 3, postId: 1, parentId: null, user: 'خالد', avatar: 'خ', text: 'ممتاز', time: 'منذ 3 دقائق', likes: 3 }
            ],
            currentPostId: null,
            replyingTo: null,

            open: function(postId) {
                this.currentPostId = postId;
                this.replyingTo = null;
                this.render();
                document.getElementById('commentsModal').classList.add('active');
            },
            close: function() {
                document.getElementById('commentsModal').classList.remove('active');
                this.currentPostId = null;
                this.replyingTo = null;
            },
            setReply: function(commentId) {
                this.replyingTo = commentId;
                const input = document.getElementById('commentInput');
                input.focus();
                input.placeholder = 'اكتب ردك...';
            },
            render: function() {
                const list = document.getElementById('commentsList');
                if (!list) return;
                const postComments = this.comments.filter(c => c.postId === this.currentPostId);
                const topLevel = postComments.filter(c => c.parentId === null);
                const renderComment = (c, depth = 0) => {
                    const replies = postComments.filter(r => r.parentId === c.id);
                    return `
                        <div class="comment-item" style="margin-right: ${depth * 20}px;">
                            <div class="comment-main">
                                <div class="comment-avatar">${c.avatar}</div>
                                <div class="comment-content">
                                    <div class="comment-header">
                                        <span class="comment-username">${c.user}</span>
                                        <span class="comment-time">${c.time}</span>
                                    </div>
                                    <div class="comment-text">${c.text}</div>
                                    <div class="comment-actions">
                                        <button class="comment-action" onclick="CommentsSystem.like(${c.id})"><i class="fas fa-heart"></i> ${c.likes}</button>
                                        <button class="comment-action" onclick="CommentsSystem.setReply(${c.id})"><i class="fas fa-reply"></i> رد</button>
                                    </div>
                                </div>
                            </div>
                            ${replies.map(r => renderComment(r, depth + 1)).join('')}
                        </div>
                    `;
                };
                list.innerHTML = topLevel.map(c => renderComment(c, 0)).join('');
            },
            addComment: function() {
                const input = document.getElementById('commentInput');
                const text = input.value.trim();
                if (!text) return;
                const newComment = {
                    id: Date.now(),
                    postId: this.currentPostId,
                    parentId: this.replyingTo,
                    user: AuthSystem.currentUser.name,
                    avatar: AuthSystem.currentUser.avatar,
                    text: text,
                    time: 'الآن',
                    likes: 0
                };
                this.comments.push(newComment);
                this.replyingTo = null;
                this.render();
                input.value = '';
                input.placeholder = 'أضف تعليقاً...';
                UI.showToast('✅ تم إضافة التعليق', 'success');
            },
            like: function(id) {
                const comment = this.comments.find(c => c.id === id);
                if (comment) {
                    comment.likes++;
                    this.render();
                }
            }
        };

        const ReelsSystem = {
            videos: [
                { id: 1, url: 'https://www.w3schools.com/html/mov_bbb.mp4', user: 'محمد', avatar: 'م', likes: 1200 },
                { id: 2, url: 'https://www.w3schools.com/html/movie.mp4', user: 'سارة', avatar: 'س', likes: 3400 }
            ],
            openFullscreen: function(videoId) {
                const container = document.getElementById('fullscreenReelsContainer');
                if (!container) return;
                container.innerHTML = '';
                this.videos.forEach(v => {
                    const item = document.createElement('div');
                    item.className = 'reel-item';
                    item.innerHTML = `<video src="${v.url}" loop playsinline></video>`;
                    container.appendChild(item);
                });
                document.getElementById('reelsFullscreen').classList.add('active');
                setTimeout(() => {
                    document.querySelectorAll('#fullscreenReelsContainer video').forEach((v, i) => {
                        if (i === 0) v.play().catch(e => {});
                    });
                }, 100);
            },
            closeFullscreen: function() {
                document.getElementById('reelsFullscreen').classList.remove('active');
                document.querySelectorAll('#fullscreenReelsContainer video').forEach(v => v.pause());
            }
        };
    </script>

    <!-- =============== القسم 3: أنظمة التفاعل (مع تحديث LiveSystem لدعم الهدايا) =============== -->
    <script>
        const NotificationSystem = {
            notifications: [
                { id: 1, icon: '❤️', title: 'إعجاب جديد', message: 'أعجب محمد بمنشورك', time: 'منذ 5 دقائق', read: false },
                { id: 2, icon: '💬', title: 'تعليق جديد', message: 'علق سارة على منشورك', time: 'منذ 10 دقائق', read: false },
                { id: 3, icon: '👥', title: 'متابعة جديدة', message: 'بدأ أحمد بمتابعتك', time: 'منذ 15 دقيقة', read: true }
            ],
            show: function() { this.render(); document.getElementById('notificationCenter').classList.add('active'); },
            hide: function() { document.getElementById('notificationCenter').classList.remove('active'); },
            goBack: function() { this.hide(); Navigation.switchPage('home'); },
            render: function() {
                const list = document.getElementById('notificationList');
                if (!list) return;
                list.innerHTML = this.notifications.map(n => `
                    <div class="notification-item ${n.read ? '' : 'unread'}">
                        <div class="notification-icon">${n.icon}</div>
                        <div class="notification-content">
                            <div class="notification-title">${n.title}</div>
                            <div class="notification-message">${n.message}</div>
                            <div class="notification-time">${n.time}</div>
                        </div>
                    </div>
                `).join('');
            },
            markAllRead: function() {
                this.notifications.forEach(n => n.read = true);
                this.render();
                UI.showToast('✅ تم تعليم الكل كمقروء', 'success');
            }
        };

        const SearchSystem = {
            performSearch: function() { 
                const query = document.getElementById('searchInput')?.value;
                UI.showToast(`🔍 بحث عن: ${query || 'الكل'}`, 'info'); 
            },
            filter: function(f, e) { 
                document.querySelectorAll('.filter-chip').forEach(c => c.classList.remove('active')); 
                e.classList.add('active'); 
                UI.showToast(`تصفية: ${f}`, 'info'); 
            }
        };

        const MessagesSystem = {
            conversations: [],
            currentConversationId: null,
            init: function() {
                this.createConversation('user_1', 'user_2', [
                    { from: 'user_2', text: 'مرحباً محمد! كيف حالك؟', time: '10:30', read: true, type: 'text' },
                    { from: 'user_1', text: 'أهلاً سارة، بخير الحمدلله', time: '10:32', read: true, type: 'text' },
                ]);
                this.createConversation('user_1', 'user_3', [
                    { from: 'user_3', text: 'هل رأيت المشروع الجديد؟', time: '09:15', read: false, type: 'text' },
                ]);
            },
            createConversation: function(userId1, userId2, initialMessages = []) {
                const id = 'conv_' + Date.now() + '_' + Math.random();
                const conversation = {
                    id: id,
                    participants: [userId1, userId2],
                    messages: initialMessages.map(m => ({
                        ...m,
                        id: IDGenerator.generate('msg'),
                        time: m.time || new Date().toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' })
                    }))
                };
                this.conversations.push(conversation);
                return conversation;
            },
            getConversationBetween: function(userId1, userId2) {
                return this.conversations.find(c => c.participants.includes(userId1) && c.participants.includes(userId2));
            },
            getOrCreateConversation: function(userId1, userId2) {
                let conv = this.getConversationBetween(userId1, userId2);
                if (!conv) conv = this.createConversation(userId1, userId2, []);
                return conv;
            },
            openChatWithUser: function(otherUserId) {
                if (!otherUserId || otherUserId === AuthSystem.currentUser.id) {
                    UI.showToast('لا يمكن مراسلة نفسك', 'error');
                    return;
                }
                const otherUser = UsersManager.getUserById(otherUserId);
                if (!otherUser) return;
                const conv = this.getOrCreateConversation(AuthSystem.currentUser.id, otherUserId);
                this.currentConversationId = conv.id;
                document.getElementById('chatWindowUserName').textContent = otherUser.name;
                document.getElementById('chatWindowUserStatus').textContent = 'متصل';
                document.getElementById('callUserName').textContent = otherUser.name;
                this.renderMessages(conv);
                document.getElementById('chatWindowModal').style.display = 'flex';
            },
            closeChatWindow: function() {
                document.getElementById('chatWindowModal').style.display = 'none';
                this.currentConversationId = null;
            },
            renderMessages: function(conv) {
                const container = document.getElementById('chatWindowMessages');
                container.innerHTML = '';
                conv.messages.forEach(msg => {
                    const isMine = msg.from === AuthSystem.currentUser.id;
                    const div = document.createElement('div');
                    div.style.cssText = `display:flex; ${isMine ? 'justify-content:flex-end' : 'justify-content:flex-start'}; margin-bottom:10px;`;
                    let contentHtml = '';
                    if (msg.type === 'text') contentHtml = `<div>${msg.text}</div>`;
                    else if (msg.type === 'image') contentHtml = `<img src="${msg.text}" style="max-width:200px; max-height:200px; border-radius:10px;" onclick="window.open('${msg.text}')">`;
                    else if (msg.type === 'video') contentHtml = `<video src="${msg.text}" controls style="max-width:200px; max-height:200px; border-radius:10px;"></video>`;
                    div.innerHTML = `
                        <div style="max-width:70%; padding:10px; border-radius:15px; background:${isMine ? 'var(--primary-600)' : 'var(--dark-200)'}; color:${isMine ? 'white' : 'var(--dark-900)'};">
                            ${contentHtml}
                            <div style="font-size:10px; opacity:0.7; margin-top:5px; text-align:left;">${msg.time}</div>
                        </div>
                    `;
                    container.appendChild(div);
                });
                container.scrollTop = container.scrollHeight;
            },
            sendMessage: function() {
                const input = document.getElementById('chatWindowInput');
                const text = input.value.trim();
                if (!text) return;
                const conv = this.conversations.find(c => c.id === this.currentConversationId);
                if (!conv) return;
                const newMsg = {
                    id: IDGenerator.generate('msg'),
                    from: AuthSystem.currentUser.id,
                    to: conv.participants.find(p => p !== AuthSystem.currentUser.id),
                    text: text,
                    time: new Date().toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' }),
                    read: false,
                    type: 'text'
                };
                conv.messages.push(newMsg);
                this.renderMessages(conv);
                input.value = '';
                setTimeout(() => {
                    const otherUserId = conv.participants.find(p => p !== AuthSystem.currentUser.id);
                    const replyMsg = {
                        id: IDGenerator.generate('msg'),
                        from: otherUserId,
                        to: AuthSystem.currentUser.id,
                        text: 'تم استلام رسالتك، شكراً!',
                        time: new Date().toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' }),
                        read: false,
                        type: 'text'
                    };
                    conv.messages.push(replyMsg);
                    this.renderMessages(conv);
                    UI.showToast('📩 رسالة جديدة', 'info');
                }, 2000);
            },
            startVoiceCall: function() {
                const otherUserId = this.conversations.find(c => c.id === this.currentConversationId)?.participants.find(p => p !== AuthSystem.currentUser.id);
                const otherUser = otherUserId ? UsersManager.getUserById(otherUserId) : null;
                if (otherUser) {
                    document.getElementById('callUserName').textContent = otherUser.name;
                    document.getElementById('callNotification').style.display = 'block';
                    setTimeout(() => { document.getElementById('callNotification').style.display = 'none'; }, 5000);
                }
            },
            attachMedia: function() {
                const input = document.createElement('input');
                input.type = 'file';
                input.accept = 'image/*,video/*';
                input.onchange = (e) => {
                    const file = e.target.files[0];
                    if (!file) return;
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        const conv = this.conversations.find(c => c.id === this.currentConversationId);
                        if (!conv) return;
                        const mediaType = file.type.startsWith('image/') ? 'image' : 'video';
                        const newMsg = {
                            id: IDGenerator.generate('msg'),
                            from: AuthSystem.currentUser.id,
                            to: conv.participants.find(p => p !== AuthSystem.currentUser.id),
                            text: e.target.result,
                            time: new Date().toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' }),
                            read: false,
                            type: mediaType
                        };
                        conv.messages.push(newMsg);
                        this.renderMessages(conv);
                    };
                    reader.readAsDataURL(file);
                };
                input.click();
            },
            getConversationsForUser: function(userId) {
                return this.conversations.filter(c => c.participants.includes(userId));
            }
        };

        const ChatSystem = {
            init: function() { MessagesSystem.init(); this.loadChatList(); },
            loadChatList: function() {
                const list = document.getElementById('chatList');
                if (!list) return;
                const conversations = MessagesSystem.getConversationsForUser(AuthSystem.currentUser.id);
                list.innerHTML = '';
                if (conversations.length === 0) {
                    list.innerHTML = '<div style="padding:20px; text-align:center; color:var(--dark-500);">لا توجد محادثات بعد</div>';
                    return;
                }
                conversations.forEach(conv => {
                    const otherUserId = conv.participants.find(p => p !== AuthSystem.currentUser.id);
                    const otherUser = UsersManager.getUserById(otherUserId);
                    if (!otherUser) return;
                    const lastMsg = conv.messages[conv.messages.length - 1];
                    const unreadCount = conv.messages.filter(m => m.to === AuthSystem.currentUser.id && !m.read).length;
                    const item = document.createElement('div');
                    item.className = 'chat-item';
                    item.style.cssText = 'display:flex; align-items:center; padding:15px; border-bottom:1px solid var(--dark-200); cursor:pointer;';
                    item.onclick = () => MessagesSystem.openChatWithUser(otherUserId);
                    item.innerHTML = `
                        <div class="chat-avatar" style="width:50px; height:50px; border-radius:50%; background:var(--primary-100); display:flex; align-items:center; justify-content:center; font-size:24px; margin-left:10px;">${otherUser.avatar}</div>
                        <div style="flex:1;">
                            <div style="display:flex; justify-content:space-between;">
                                <span style="font-weight:600;">${otherUser.name}</span>
                                <span style="font-size:12px; color:var(--dark-500);">${lastMsg ? lastMsg.time : ''}</span>
                            </div>
                            <div style="display:flex; justify-content:space-between;">
                                <span style="font-size:14px; color:var(--dark-600);">${lastMsg ? (lastMsg.from === AuthSystem.currentUser.id ? 'أنت: ' : '') + (lastMsg.type === 'text' ? lastMsg.text : (lastMsg.type === 'image' ? '🖼️ صورة' : '🎥 فيديو')) : ''}</span>
                                ${unreadCount > 0 ? `<span style="background:var(--primary-600); color:white; border-radius:50%; padding:2px 6px; font-size:12px;">${unreadCount}</span>` : ''}
                            </div>
                        </div>
                    `;
                    list.appendChild(item);
                });
            }
        };

        // نظام البث المباشر مع الهدايا
        const LiveSystem = {
            currentStreamId: null,
            gifts: [
                { id: 1, name: 'وردة', icon: '🌹', price: 10 },
                { id: 2, name: 'قلب', icon: '❤️', price: 20 },
                { id: 3, name: 'تاج', icon: '👑', price: 50 },
                { id: 4, name: 'سيارة', icon: '🚗', price: 100 },
                { id: 5, name: 'طائرة', icon: '✈️', price: 200 },
                { id: 6, name: 'صاروخ', icon: '🚀', price: 500 }
            ],
            loadLiveStreams: function() {
                const grid = document.getElementById('liveGrid');
                if (grid) grid.innerHTML = `
                    <div class="live-card" onclick="LiveSystem.openStream('stream1')">
                        <img src="https://picsum.photos/200/300?random=1">
                        <div class="live-badge"><i class="fas fa-circle"></i> مباشر</div>
                        <div class="live-viewer-count">1.2k</div>
                    </div>
                    <div class="live-card" onclick="LiveSystem.openStream('stream2')">
                        <img src="https://picsum.photos/200/300?random=2">
                        <div class="live-badge"><i class="fas fa-circle"></i> مباشر</div>
                        <div class="live-viewer-count">3.4k</div>
                    </div>
                `;
                this.renderGifts();
            },
            renderGifts: function() {
                const grid = document.getElementById('giftsGrid');
                if (!grid) return;
                grid.innerHTML = this.gifts.map(g => `
                    <div class="gift-item" onclick="LiveSystem.sendGift(${g.id})" style="text-align: center; padding: 10px; border: 1px solid var(--dark-200); border-radius: var(--radius-lg); cursor: pointer;">
                        <div style="font-size: 32px;">${g.icon}</div>
                        <div style="font-size: 14px; font-weight: 600;">${g.name}</div>
                        <div style="font-size: 12px; color: var(--primary-600);">${g.price} نقطة</div>
                    </div>
                `).join('');
            },
            startLive: function() { UI.showToast('❌ تحتاج 1000 متابع للبث', 'error'); },
            openStream: function(id) {
                this.currentStreamId = id;
                document.getElementById('liveStreamModal').classList.add('active');
                WalletSystem.updateUI(); // تحديث الرصيد المعروض
            },
            closeStream: function() {
                document.getElementById('liveStreamModal').classList.remove('active');
                this.currentStreamId = null;
            },
            toggleGifts: function() {
                document.getElementById('liveGiftsSection').classList.toggle('active');
            },
            sendGift: function(giftId) {
                const gift = this.gifts.find(g => g.id === giftId);
                if (!gift) return;
                WalletSystem.deduct(gift.price, (success) => {
                    if (success) {
                        // إظهار إشعار في البث
                        const notification = document.createElement('div');
                        notification.style.cssText = 'position: absolute; bottom: 100px; left: 20px; background: rgba(0,0,0,0.7); color: white; padding: 10px; border-radius: 20px; animation: fadeOut 3s forwards;';
                        notification.innerHTML = `<i class="fas fa-gift"></i> ${AuthSystem.currentUser.name} أرسل ${gift.icon} ${gift.name}`;
                        document.querySelector('.live-stream-video').appendChild(notification);
                        setTimeout(() => notification.remove(), 3000);
                        UI.showToast(`🎁 أرسلت ${gift.name} إلى البث`, 'success');
                    }
                });
            }
        };
    </script>
<!-- نهاية الرد الثالث (أ) --><!-- =============== القسم 4: نظام السوق =============== -->
    <script>
        const MarketSystem = {
            currentCountry: 'JO',
            storePrice: null,
            stores: {},
            currentStoreId: null,
            storesPerPage: 20,
            currentPage: 1,
            isLoading: false,
            hasMore: true,
            observer: null,
            init: function() { this.generateMockStores(500); this.renderStores(); this.renderProducts(); this.updateSelectedCountryName(); },
            generateMockStores: function(countPerCountry = 500) {
                const countryNames = {
                    'JO': 'الأردن', 'SA': 'السعودية', 'QA': 'قطر', 'KW': 'الكويت', 'AE': 'الإمارات',
                    'US': 'أمريكا', 'GB': 'بريطانيا', 'DE': 'ألمانيا', 'FR': 'فرنسا', 'CA': 'كندا',
                    'AU': 'أستراليا', 'JP': 'اليابان', 'KR': 'كوريا', 'TR': 'تركيا', 'BH': 'البحرين',
                    'OM': 'عمان', 'EG': 'مصر', 'IQ': 'العراق', 'MA': 'المغرب', 'TN': 'تونس',
                    'LB': 'لبنان', 'PS': 'فلسطين', 'YE': 'اليمن', 'SD': 'السودان', 'LY': 'ليبيا',
                    'DZ': 'الجزائر', 'MR': 'موريتانيا', 'SO': 'الصومال', 'DJ': 'جيبوتي', 'KM': 'جزر القمر'
                };
                for (const country in countryNames) {
                    const storesArray = [];
                    for (let i = 1; i <= countPerCountry; i++) {
                        const randomProducts = Math.floor(Math.random() * 100) + 20;
                        const randomFollowers = Math.floor(Math.random() * 10000) + 500;
                        const randomRating = (Math.random() * 2 + 3).toFixed(1);
                        const creationDate = new Date(Date.now() - Math.random() * 30 * 24 * 60 * 60 * 1000);
                        const expiryDate = new Date(creationDate.getTime() + 30 * 24 * 60 * 60 * 1000);
                        const shelves = [];
                        const numShelves = Math.floor(Math.random() * 5) + 2;
                        for (let s = 1; s <= numShelves; s++) {
                            shelves.push({
                                id: `shelf_${country}_${i}_${s}`,
                                name: `رف ${s}`,
                                products: this.generateMockProducts(country, Math.floor(randomProducts / numShelves) + 1)
                            });
                        }
                        storesArray.push({
                            id: `store_${country}_${i}`,
                            name: `متجر ${countryNames[country]} ${i}`,
                            logo: this.getCountryFlag(country),
                            products: randomProducts,
                            followers: randomFollowers,
                            rating: parseFloat(randomRating),
                            owner: `user_${country}_${i}`,
                            description: `أفضل المنتجات من ${countryNames[country]} - متجر رقم ${i}`,
                            createdAt: creationDate,
                            expiryDate: expiryDate,
                            shelves: shelves,
                            isActive: expiryDate > new Date()
                        });
                    }
                    this.stores[country] = storesArray;
                }
            },
            generateMockProducts: function(country, count) {
                const products = [];
                const icons = ['📱', '💻', '🎧', '⌚', '🕶️', '👕', '👗', '🧴', '🪔', '☕', '📿', '🧣', '👜', '👟', '🎒'];
                for (let i = 1; i <= count; i++) {
                    products.push({
                        id: `prod_${country}_${i}_${Date.now()}_${i}`,
                        name: `منتج ${i}`,
                        price: Math.floor(Math.random() * 900) + 50,
                        image: icons[Math.floor(Math.random() * icons.length)]
                    });
                }
                return products;
            },
            getCountryFlag: function(country) {
                const flags = {
                    'JO': '🇯🇴', 'SA': '🇸🇦', 'QA': '🇶🇦', 'KW': '🇰🇼', 'AE': '🇦🇪',
                    'US': '🇺🇸', 'GB': '🇬🇧', 'DE': '🇩🇪', 'FR': '🇫🇷', 'CA': '🇨🇦',
                    'AU': '🇦🇺', 'JP': '🇯🇵', 'KR': '🇰🇷', 'TR': '🇹🇷', 'BH': '🇧🇭',
                    'OM': '🇴🇲', 'EG': '🇪🇬', 'IQ': '🇮🇶', 'MA': '🇲🇦', 'TN': '🇹🇳',
                    'LB': '🇱🇧', 'PS': '🇵🇸', 'YE': '🇾🇪', 'SD': '🇸🇩', 'LY': '🇱🇾',
                    'DZ': '🇩🇿', 'MR': '🇲🇷', 'SO': '🇸🇴', 'DJ': '🇩🇯', 'KM': '🇰🇲'
                };
                return flags[country] || '🏳️';
            },
            changeCountry: function() {
                const select = document.getElementById('countrySelect');
                this.currentCountry = select.value;
                PromoSystem.currentCountry = this.currentCountry;
                this.updateStorePrice();
                this.currentPage = 1;
                this.hasMore = true;
                this.renderStores(true);
                this.renderProducts();
                this.updateSelectedCountryName();
                UI.showToast('🌡️ تم اختيار الدولة، يمكنك الآن استعراض المتاجر', 'info');
            },
            updateSelectedCountryName: function() {
                const select = document.getElementById('countrySelect');
                const countryName = select.options[select.selectedIndex]?.text.split(' - ')[0] || 'الدولة';
                document.getElementById('selectedCountryName').textContent = countryName;
            },
            updateStorePrice: function() {
                const developed = ['JO','TR','QA','SA','KW','AE','US','GB','DE','FR','CA','AU','JP','KR'];
                this.storePrice = developed.includes(this.currentCountry) ? 100 : 25;
                const basic = document.getElementById('storePlanPrice');
                const pro = document.getElementById('storePlanPricePro');
                const premium = document.getElementById('storePlanPricePremium');
                if (basic) basic.innerHTML = '$' + this.storePrice + ' <small>/شهر</small>';
                if (pro) pro.innerHTML = '$' + (this.storePrice * 2.5) + ' <small>/شهر</small>';
                if (premium) premium.innerHTML = '$' + (this.storePrice * 5) + ' <small>/شهر</small>';
            },
            renderStores: function(reset = false) {
                const grid = document.getElementById('storesGrid');
                if (!grid) return;
                const storesForCountry = (this.stores[this.currentCountry] || []).filter(store => store.isActive);
                if (storesForCountry.length === 0) {
                    grid.innerHTML = '<div class="no-stores">لا توجد متاجر نشطة في هذه الدولة حالياً</div>';
                    return;
                }
                if (reset) { grid.innerHTML = ''; this.currentPage = 1; this.hasMore = true; }
                const start = (this.currentPage - 1) * this.storesPerPage;
                const end = start + this.storesPerPage;
                const storesToShow = storesForCountry.slice(start, end);
                if (storesToShow.length === 0) { this.hasMore = false; return; }
                const developed = ['JO','TR','QA','SA','KW','AE','US','GB','DE','FR','CA','AU','JP','KR'];
                storesToShow.forEach(store => {
                    const card = document.createElement('div');
                    card.className = 'store-card';
                    card.setAttribute('data-store-id', store.id);
                    card.onclick = () => MarketSystem.openStore(store.id);
                    const now = new Date();
                    const daysLeft = Math.max(0, Math.ceil((store.expiryDate - now) / (1000 * 60 * 60 * 24)));
                    const subscriptionPrice = developed.includes(this.currentCountry) ? 100 : 25;
                    card.innerHTML = `
                        <div class="store-cover"></div>
                        <div class="store-logo">${store.logo}</div>
                        <div class="store-info">
                            <div class="store-name">${store.name}</div>
                            <div class="store-meta">
                                <span><i class="fas fa-box"></i> ${store.products}</span>
                                <span><i class="fas fa-users"></i> ${MarketSystem.formatNumber(store.followers)}</span>
                                <span><i class="fas fa-star" style="color: gold;"></i> ${store.rating}</span>
                            </div>
                            <div class="store-expiry" style="font-size: 11px; color: ${daysLeft < 5 ? 'var(--danger-600)' : 'var(--warning-600)'};">
                                <i class="fas fa-hourglass-half"></i> متبقي: ${daysLeft} يوم
                            </div>
                            <div class="store-price" style="font-size: 12px; margin-top: 5px; color: var(--primary-600);">
                                <i class="fas fa-tag"></i> اشتراك: $${subscriptionPrice}/شهر
                            </div>
                        </div>
                    `;
                    grid.appendChild(card);
                });
                this.setupInfiniteScroll();
            },
            setupInfiniteScroll: function() {
                const grid = document.getElementById('storesGrid');
                if (!grid) return;
                if (this.observer) this.observer.disconnect();
                this.observer = new IntersectionObserver((entries) => {
                    entries.forEach(entry => {
                        if (entry.isIntersecting && this.hasMore && !this.isLoading) this.loadMoreStores();
                    });
                }, { threshold: 0.1, rootMargin: '100px' });
                const lastCard = grid.lastElementChild;
                if (lastCard) this.observer.observe(lastCard);
            },
            loadMoreStores: function() {
                if (this.isLoading || !this.hasMore) return;
                this.isLoading = true;
                setTimeout(() => {
                    this.currentPage++;
                    const storesForCountry = (this.stores[this.currentCountry] || []).filter(store => store.isActive);
                    const start = (this.currentPage - 1) * this.storesPerPage;
                    const end = start + this.storesPerPage;
                    const moreStores = storesForCountry.slice(start, end);
                    if (moreStores.length === 0) { this.hasMore = false; this.isLoading = false; return; }
                    const grid = document.getElementById('storesGrid');
                    const developed = ['JO','TR','QA','SA','KW','AE','US','GB','DE','FR','CA','AU','JP','KR'];
                    moreStores.forEach(store => {
                        const now = new Date();
                        const daysLeft = Math.max(0, Math.ceil((store.expiryDate - now) / (1000 * 60 * 60 * 24)));
                        const subscriptionPrice = developed.includes(this.currentCountry) ? 100 : 25;
                        const card = document.createElement('div');
                        card.className = 'store-card';
                        card.setAttribute('data-store-id', store.id);
                        card.onclick = () => MarketSystem.openStore(store.id);
                        card.innerHTML = `
                            <div class="store-cover"></div>
                            <div class="store-logo">${store.logo}</div>
                            <div class="store-info">
                                <div class="store-name">${store.name}</div>
                                <div class="store-meta">
                                    <span><i class="fas fa-box"></i> ${store.products}</span>
                                    <span><i class="fas fa-users"></i> ${MarketSystem.formatNumber(store.followers)}</span>
                                    <span><i class="fas fa-star" style="color: gold;"></i> ${store.rating}</span>
                                </div>
                                <div class="store-expiry" style="font-size: 11px; color: ${daysLeft < 5 ? 'var(--danger-600)' : 'var(--warning-600)'};">
                                    <i class="fas fa-hourglass-half"></i> متبقي: ${daysLeft} يوم
                                </div>
                                <div class="store-price" style="font-size: 12px; margin-top: 5px; color: var(--primary-600);">
                                    <i class="fas fa-tag"></i> اشتراك: $${subscriptionPrice}/شهر
                                </div>
                            </div>
                        `;
                        grid.appendChild(card);
                    });
                    this.setupInfiniteScroll();
                    this.isLoading = false;
                }, 500);
            },
            renderProducts: function() {
                const grid = document.getElementById('productsGrid');
                if (!grid) return;
                const storesForCountry = (this.stores[this.currentCountry] || []).filter(store => store.isActive);
                if (storesForCountry.length === 0) { grid.innerHTML = '<div class="no-products">لا توجد منتجات في هذه الدولة</div>'; return; }
                let allProducts = [];
                storesForCountry.forEach(store => {
                    store.shelves.forEach(shelf => {
                        const randomProducts = shelf.products.sort(() => 0.5 - Math.random()).slice(0, 2);
                        allProducts = allProducts.concat(randomProducts);
                    });
                });
                const shuffled = allProducts.sort(() => 0.5 - Math.random()).slice(0, 30);
                grid.innerHTML = shuffled.map(p => `
                    <div class="product-card" onclick="MarketSystem.buyProduct('${p.name}', ${p.price})">
                        <div class="product-image">${p.image}</div>
                        <div class="product-info">
                            <div class="product-title">${p.name}</div>
                            <div class="product-price">$${p.price}</div>
                        </div>
                    </div>
                `).join('');
            },
            openStore: function(storeId) {
                let store = null;
                for (const country in this.stores) {
                    const found = this.stores[country].find(s => s.id === storeId);
                    if (found) { store = found; break; }
                }
                if (!store) return;
                if (!store.isActive) { UI.showToast('❌ هذا المتجر مغلق بسبب انتهاء الاشتراك', 'error'); return; }
                this.currentStoreId = storeId;
                document.getElementById('storeDetailLogo').textContent = store.logo;
                document.getElementById('storeDetailName').textContent = store.name;
                const now = new Date();
                const daysLeft = Math.max(0, Math.ceil((store.expiryDate - now) / (1000 * 60 * 60 * 24)));
                document.getElementById('storeDetailMeta').innerHTML = `
                    <i class="fas fa-box"></i> ${store.products} منتج • 
                    <i class="fas fa-users"></i> ${this.formatNumber(store.followers)} متابع •
                    <i class="fas fa-star" style="color: gold;"></i> ${store.rating} •
                    <i class="fas fa-hourglass-half" style="color: ${daysLeft < 5 ? 'var(--danger-600)' : 'var(--warning-600)'};"></i> ${daysLeft} يوم متبقي
                `;
                const manageBtn = document.getElementById('manageStoreBtn');
                if (AuthSystem.currentUser && store.owner === AuthSystem.currentUser.id) manageBtn.style.display = 'flex';
                else manageBtn.style.display = 'none';
                this.renderStoreShelves(store);
                Navigation.switchPage('storeDetail');
            },
            renderStoreShelves: function(store) {
                const grid = document.getElementById('storeProductsGrid');
                if (!grid) return;
                let html = '';
                store.shelves.forEach((shelf, index) => {
                    html += `<div class="shelf-title" style="grid-column: span 2; font-weight: bold; margin-top: ${index === 0 ? '0' : '15px'}; padding: 8px; background: var(--primary-50); border-radius: var(--radius-lg);">
                        <i class="fas fa-layer-group"></i> ${shelf.name}
                    </div>`;
                    shelf.products.forEach(p => {
                        html += `
                            <div class="store-product-card" onclick="MarketSystem.buyProduct('${p.name}', ${p.price})">
                                <div class="store-product-image">${p.image}</div>
                                <div class="store-product-info">
                                    <div class="store-product-title">${p.name}</div>
                                    <div class="store-product-price">$${p.price}</div>
                                </div>
                            </div>
                        `;
                    });
                });
                grid.innerHTML = html;
            },
            openManageStore: function() {
                const store = Object.values(this.stores).flat().find(s => s.id === this.currentStoreId);
                if (!store) return;
                document.getElementById('manageProductCount').textContent = store.products;
                document.getElementById('manageOrderCount').textContent = Math.floor(Math.random() * 30) + 5;
                document.getElementById('manageRevenue').textContent = '$' + (Math.floor(Math.random() * 5000) + 1000);
                this.renderManageProducts(store);
                Navigation.switchPage('manageStore');
            },
            renderManageProducts: function(store) {
                const list = document.getElementById('manageProductsList');
                if (!list) return;
                let allProducts = [];
                store.shelves.forEach(shelf => allProducts = allProducts.concat(shelf.products));
                list.innerHTML = allProducts.map(p => `
                    <div class="manage-product-item">
                        <div class="manage-product-image">${p.image}</div>
                        <div class="manage-product-details">
                            <div class="manage-product-name">${p.name}</div>
                            <div class="manage-product-price">
                                <input type="number" class="manage-price-input" id="price_${p.id}" value="${p.price}" min="0" step="1">
                                <button class="manage-save-price" onclick="MarketSystem.updateProductPrice('${p.id}', this)"><i class="fas fa-save"></i></button>
                            </div>
                        </div>
                        <button class="manage-delete-product" onclick="MarketSystem.deleteProduct('${p.id}')"><i class="fas fa-trash"></i></button>
                    </div>
                `).join('');
            },
            updateProductPrice: function(productId, btn) {
                const input = document.getElementById(`price_${productId}`);
                const newPrice = parseFloat(input.value);
                if (isNaN(newPrice) || newPrice < 0) { UI.showToast('❌ أدخل سعراً صالحاً', 'error'); return; }
                const store = Object.values(this.stores).flat().find(s => s.id === this.currentStoreId);
                if (store) {
                    let product = null;
                    for (const shelf of store.shelves) {
                        product = shelf.products.find(p => p.id == productId);
                        if (product) break;
                    }
                    if (product) { product.price = newPrice; UI.showToast('✅ تم تحديث السعر', 'success'); }
                }
            },
            deleteProduct: function(productId) {
                if (!confirm('هل أنت متأكد من حذف هذا المنتج؟')) return;
                const store = Object.values(this.stores).flat().find(s => s.id === this.currentStoreId);
                if (store) {
                    for (const shelf of store.shelves) {
                        const index = shelf.products.findIndex(p => p.id == productId);
                        if (index !== -1) {
                            shelf.products.splice(index, 1);
                            store.products = store.shelves.reduce((sum, s) => sum + s.products.length, 0);
                            break;
                        }
                    }
                    UI.showToast('✅ تم حذف المنتج', 'success');
                    this.renderManageProducts(store);
                    document.getElementById('manageProductCount').textContent = store.products;
                }
            },
            addProduct: function() {
                const name = prompt('أدخل اسم المنتج الجديد:');
                if (!name) return;
                const price = parseFloat(prompt('أدخل السعر:', '0'));
                if (isNaN(price) || price < 0) { UI.showToast('❌ سعر غير صالح', 'error'); return; }
                const shelfName = prompt('أدخل اسم الرف (مثلاً: إلكترونيات):', 'رف جديد') || 'رف جديد';
                const store = Object.values(this.stores).flat().find(s => s.id === this.currentStoreId);
                if (store) {
                    const shelf = store.shelves[0] || { id: 'shelf_new', name: 'الرف الرئيسي', products: [] };
                    if (!store.shelves.includes(shelf)) store.shelves.push(shelf);
                    const newId = Date.now();
                    shelf.products.push({ id: newId, name: name, price: price, image: '📦' });
                    store.products = store.shelves.reduce((sum, s) => sum + s.products.length, 0);
                    UI.showToast('✅ تم إضافة المنتج', 'success');
                    this.renderManageProducts(store);
                    document.getElementById('manageProductCount').textContent = store.products;
                }
            },
            editStoreInfo: function() {
                const store = Object.values(this.stores).flat().find(s => s.id === this.currentStoreId);
                if (!store) return;
                const newName = prompt('تعديل اسم المتجر:', store.name);
                if (newName) store.name = newName;
                const newDesc = prompt('تعديل الوصف:', store.description || '');
                if (newDesc) store.description = newDesc;
                UI.showToast('✅ تم تحديث معلومات المتجر', 'success');
                document.getElementById('storeDetailName').textContent = store.name;
            },
            buyProduct: function(name, price) { UI.showToast(`🛒 شراء ${name} بسعر $${price} - قريباً`, 'info'); },
            formatNumber: function(num) {
                if (num >= 1000000) return (num/1000000).toFixed(1) + 'م';
                if (num >= 1000) return (num/1000).toFixed(1) + 'ألف';
                return num;
            },
            openStoreRegistration: function() { document.getElementById('storeRegModal').classList.add('active'); this.updateStorePrice(); this.updateSelectedCountryName(); },
            closeStoreRegistration: function() { document.getElementById('storeRegModal').classList.remove('active'); },
            selectPlan: function(plan) { document.querySelectorAll('.plan-option').forEach(p => p.classList.remove('selected')); event.currentTarget.classList.add('selected'); },
            completeRegistration: function() {
                const selectedPlan = document.querySelector('.plan-option.selected .plan-name')?.textContent;
                if (!selectedPlan) { UI.showToast('❌ اختر خطة اشتراك أولاً', 'error'); return; }
                const storeName = document.getElementById('storeName').value.trim();
                if (!storeName) { UI.showToast('❌ أدخل اسم المتجر', 'error'); return; }
                const newStore = {
                    id: 'store_' + AuthSystem.currentUser.id + '_' + Date.now(),
                    name: storeName,
                    logo: this.getCountryFlag(this.currentCountry),
                    products: 0,
                    followers: 0,
                    rating: 0,
                    owner: AuthSystem.currentUser.id,
                    description: 'متجر جديد',
                    createdAt: new Date(),
                    expiryDate: new Date(Date.now() + 30 * 24 * 60 * 60 * 1000),
                    isActive: true,
                    shelves: [{ id: 'shelf_1', name: 'الرف الرئيسي', products: [] }]
                };
                if (!this.stores[this.currentCountry]) this.stores[this.currentCountry] = [];
                this.stores[this.currentCountry].push(newStore);
                UI.showToast(`✅ تم فتح المتجر "${storeName}" بخطة ${selectedPlan} - ينتهي الاشتراك في 30 يوم`, 'success');
                this.closeStoreRegistration();
            },
            requestLocation: function() { document.getElementById('locationStatus').classList.add('active'); },
            manualLocation: function() {
                const address = prompt('أدخل عنوانك:');
                if (address) { document.getElementById('locationStatus').querySelector('span').textContent = address; document.getElementById('locationStatus').classList.add('active'); }
            },
            switchTab: function(tab) {
                document.querySelectorAll('.market-tab').forEach(t => t.classList.remove('active'));
                event.currentTarget.classList.add('active');
                if (tab === 'stores') {
                    document.getElementById('storesSection').classList.add('active');
                    document.getElementById('productsSection').classList.remove('active');
                } else {
                    document.getElementById('storesSection').classList.remove('active');
                    document.getElementById('productsSection').classList.add('active');
                }
            }
        };
    </script>

    <!-- =============== القسم 5: الأنظمة المساعدة =============== -->
    <script>
        const ReportsManager = {
            reports: [],
            loadReports: function() {
                const stored = localStorage.getItem('novalex_reports');
                if (stored) this.reports = JSON.parse(stored);
                else {
                    this.reports = [
                        { id: 'rep1', type: 'post', targetId: 'post1', targetName: 'منشور مسيء', reason: 'محتوى غير لائق', date: new Date().toISOString(), status: 'pending' },
                        { id: 'rep2', type: 'user', targetId: 'user_2', targetName: 'سارة علي', reason: 'تحرش', date: new Date().toISOString(), status: 'pending' }
                    ];
                    this.saveReports();
                }
                return this.reports;
            },
            saveReports: function() { localStorage.setItem('novalex_reports', JSON.stringify(this.reports)); },
            addReport: function(report) {
                report.id = 'rep_' + Date.now() + '_' + Math.random().toString(36).substr(2, 5);
                report.date = new Date().toISOString();
                report.status = 'pending';
                this.reports.push(report);
                this.saveReports();
                UI.showToast('تم استلام البلاغ', 'info');
            },
            getPendingReports: function() { return this.reports.filter(r => r.status === 'pending'); },
            resolveReport: function(reportId) { const r = this.reports.find(r => r.id === reportId); if (r) { r.status = 'resolved'; this.saveReports(); } },
            deleteReport: function(reportId) { this.reports = this.reports.filter(r => r.id !== reportId); this.saveReports(); },
            renderReportsList: function(containerId) {
                const container = document.getElementById(containerId);
                if (!container) return;
                const pending = this.getPendingReports();
                if (pending.length === 0) { container.innerHTML = '<p style="text-align: center; color: var(--dark-500);">لا توجد بلاغات جديدة</p>'; return; }
                container.innerHTML = pending.map(report => `
                    <div class="report-item">
                        <div style="display: flex; justify-content: space-between; align-items: center;">
                            <div>
                                <span style="font-weight: 600;">${report.type === 'post' ? '📄 منشور' : '👤 مستخدم'}</span>
                                <span style="color: var(--dark-500); font-size: 12px; margin-right: 10px;">${new Date(report.date).toLocaleString('ar-EG')}</span>
                            </div>
                            <span class="report-status">معلق</span>
                        </div>
                        <p style="margin: 10px 0;"><strong>المستهدف:</strong> ${report.targetName}</p>
                        <p style="margin: 5px 0;"><strong>السبب:</strong> ${report.reason}</p>
                        <div class="report-actions">
                            <button class="report-btn success" onclick="ReportsManager.resolveReport('${report.id}'); ReportsManager.renderReportsList('reportsList')">تم التعامل</button>
                            <button class="report-btn danger" onclick="ReportsManager.deleteReport('${report.id}'); ReportsManager.renderReportsList('reportsList')">حذف البلاغ</button>
                        </div>
                    </div>
                `).join('');
            }
        };

        const ReportSystem = {
            reportPost: function() {
                const postId = PostMenu.currentPostId;
                if (!postId) return;
                const reason = prompt('سبب الإبلاغ (اختياري):', 'محتوى غير لائق');
                if (reason !== null) ReportsManager.addReport({ type: 'post', targetId: postId, targetName: 'منشور رقم ' + postId, reason: reason });
                document.getElementById('postContextMenu').style.display = 'none';
                PostMenu.currentPostId = null;
            },
            reportUser: function() {
                const userId = UserProfileMenu.currentUserId;
                if (!userId) return;
                const user = UsersManager.getUserById(userId);
                if (!user) return;
                const reason = prompt('سبب الإبلاغ عن هذا المستخدم (اختياري):', 'محتوى غير لائق');
                if (reason !== null) ReportsManager.addReport({ type: 'user', targetId: userId, targetName: user.name, reason: reason });
                document.getElementById('userProfileContextMenu').style.display = 'none';
                UserProfileMenu.currentUserId = null;
            }
        };

        const BlockSystem = {
            blockedUsers: [],
            blockUser: function() {
                const username = PostMenu.currentUsername;
                if (!username) return;
                if (confirm(`هل أنت متأكد من حظر ${username}؟`)) {
                    this.blockedUsers.push(username);
                    UI.showToast(`✅ تم حظر ${username}`, 'success');
                    FeedSystem.posts = FeedSystem.posts.filter(p => p.user !== username);
                    FeedSystem.loadFeed();
                }
                document.getElementById('postContextMenu').style.display = 'none';
                PostMenu.currentUsername = null;
            },
            blockUserFromProfile: function() {
                const userId = UserProfileMenu.currentUserId || window.currentViewingUserId;
                if (!userId) return;
                const user = UsersManager.getUserById(userId);
                if (!user) return;
                if (confirm(`هل أنت متأكد من حظر ${user.name}؟`)) {
                    this.blockedUsers.push(user.username);
                    UI.showToast(`✅ تم حظر ${user.name}`, 'success');
                    FeedSystem.posts = FeedSystem.posts.filter(p => p.userId !== userId);
                    FeedSystem.loadFeed();
                    Navigation.switchPage('home');
                }
                document.getElementById('userProfileContextMenu').style.display = 'none';
                UserProfileMenu.currentUserId = null;
            }
        };

        const PostMenu = {
            currentPostId: null, currentUsername: null,
            toggle: function(element, postId, username) {
                this.currentPostId = postId; this.currentUsername = username;
                const menu = document.getElementById('postContextMenu');
                const rect = element.getBoundingClientRect();
                menu.style.display = 'block';
                menu.style.top = rect.bottom + 'px';
                menu.style.left = (rect.left - 150) + 'px';
                setTimeout(() => document.addEventListener('click', this.closeOutside), 10);
            },
            closeOutside: function(e) {
                const menu = document.getElementById('postContextMenu');
                if (!menu.contains(e.target)) { menu.style.display = 'none'; document.removeEventListener('click', PostMenu.closeOutside); }
            }
        };

        const UserProfileMenu = {
            currentUserId: null,
            toggle: function(element, userId) {
                this.currentUserId = userId;
                const menu = document.getElementById('userProfileContextMenu');
                const rect = element.getBoundingClientRect();
                menu.style.display = 'block';
                menu.style.top = rect.bottom + 'px';
                menu.style.left = (rect.left - 150) + 'px';
                setTimeout(() => document.addEventListener('click', this.closeOutside), 10);
            },
            closeOutside: function(e) {
                const menu = document.getElementById('userProfileContextMenu');
                if (!menu.contains(e.target)) { menu.style.display = 'none'; document.removeEventListener('click', UserProfileMenu.closeOutside); }
            }
        };

        const PromoSystem = {
            currentGoal: 'views', currentCountry: 'JO', basePricePerDay: 2, minPricePerDay: 2,
            showForPost: function(postId) {
                const modal = document.getElementById('promoModal');
                this.currentCountry = document.getElementById('countrySelect')?.value || 'JO';
                this.updateBasePrice();
                document.getElementById('promoPricePerDay').value = this.basePricePerDay;
                document.getElementById('promoDays').value = 1;
                this.calculateTotal();
                modal.classList.add('active');
            },
            updateBasePrice: function() {
                const developed = ['JO','TR','QA','SA','KW','AE','US','GB','DE','FR','CA','AU','JP','KR'];
                this.basePricePerDay = developed.includes(this.currentCountry) ? 5 : 2;
                this.minPricePerDay = this.basePricePerDay;
                document.getElementById('promoPricePerDay').min = this.minPricePerDay;
                document.getElementById('promoCountryHint').textContent = `السعر الأساسي: $${this.basePricePerDay} لليوم (حسب الدولة)`;
            },
            calculateTotal: function() {
                const days = parseInt(document.getElementById('promoDays').value) || 1;
                let pricePerDay = parseFloat(document.getElementById('promoPricePerDay').value) || this.minPricePerDay;
                if (pricePerDay < this.minPricePerDay) { pricePerDay = this.minPricePerDay; document.getElementById('promoPricePerDay').value = pricePerDay; }
                document.getElementById('promoTotal').textContent = '$' + (days * pricePerDay).toFixed(2);
            },
            selectGoal: function(goal) { this.currentGoal = goal; document.querySelectorAll('.goal-item').forEach(g => g.classList.remove('active')); event.currentTarget.classList.add('active'); },
            confirm: function() {
                const total = document.getElementById('promoTotal').textContent;
                const days = document.getElementById('promoDays').value;
                const pricePerDay = document.getElementById('promoPricePerDay').value;
                UI.showToast(`✅ تم تفعيل الترويج لمدة ${days} يوم بمبلغ ${total}`, 'success');
                document.getElementById('promoModal').classList.remove('active');
            },
            close: function() { document.getElementById('promoModal').classList.remove('active'); }
        };

        const CustomerService = {
            messages: [{ role: 'agent', content: 'مرحباً بك في خدمة العملاء! كيف يمكنني مساعدتك؟' }],
            toggle: function() { const chat = document.getElementById('csChat'); chat.style.display = chat.style.display === 'none' ? 'flex' : 'none'; if (chat.style.display === 'flex') this.render(); },
            hide: function() { document.getElementById('csChat').style.display = 'none'; },
            sendMessage: function() {
                const input = document.getElementById('csInput');
                const msg = input.value.trim();
                if (!msg) return;
                this.messages.push({ role: 'user', content: msg });
                this.render();
                input.value = '';
                setTimeout(() => {
                    let response = 'شكراً لتواصلك. سيتم الرد قريباً.';
                    if (msg.includes('مشكلة') || msg.includes('خطأ')) response = 'هذه مشكلة تقنية. دعني أساعدك في حلها... يرجى توضيح التفاصيل.';
                    this.messages.push({ role: 'agent', content: response });
                    this.render();
                }, 1000);
            },
            render: function() {
                const container = document.getElementById('csMessages');
                if (!container) return;
                container.innerHTML = this.messages.map(m => m.role === 'agent' 
                    ? `<div class="ai-message"><div class="ai-avatar-small">👤</div><div class="ai-bubble">${m.content}</div></div>`
                    : `<div style="display:flex; justify-content:flex-end; margin-bottom:10px;"><div class="user-bubble">${m.content}</div></div>`
                ).join('');
            }
        };

        const DeepSeekAI = {
            messages: [{ role: 'ai', content: 'مرحباً! أنا DeepSeek AI. كيف يمكنني مساعدتك؟' }],
            toggle: function() { const chat = document.getElementById('aiChat'); chat.style.display = chat.style.display === 'none' ? 'flex' : 'none'; if (chat.style.display === 'flex') this.render(); },
            hide: function() { document.getElementById('aiChat').style.display = 'none'; },
            sendMessage: function() {
                const input = document.getElementById('aiInput');
                const msg = input.value.trim();
                if (!msg) return;
                this.messages.push({ role: 'user', content: msg });
                this.render();
                input.value = '';
                setTimeout(() => {
                    let response = 'شكراً لسؤالك! أنا هنا لمساعدتك.';
                    if (msg.includes('شكر')) response = 'العفو! دائمًا في الخدمة 🤗';
                    else if (msg.includes('المنصة') || msg.includes('تطبيق')) response = 'نوفالكس هي منصة متكاملة تجمع بين التواصل الاجتماعي والتسويق الإلكتروني.';
                    this.messages.push({ role: 'ai', content: response });
                    this.render();
                }, 1000);
            },
            render: function() {
                const container = document.getElementById('aiMessages');
                if (!container) return;
                container.innerHTML = this.messages.map(m => m.role === 'ai'
                    ? `<div class="ai-message"><div class="ai-avatar-small">🤖</div><div class="ai-bubble">${m.content}</div></div>`
                    : `<div style="display:flex; justify-content:flex-end; margin-bottom:10px;"><div class="user-bubble">${m.content}</div></div>`
                ).join('');
            }
        };
    </script>

    <!-- =============== القسم 6: الملف الشخصي والإعدادات والتهيئة =============== -->
    <script>
        const ProfileSystem = {
            editName: function() { 
                const newName = prompt('أدخل الاسم الجديد:', AuthSystem.currentUser.name);
                if (newName) { AuthSystem.currentUser.name = newName; document.getElementById('profileName').textContent = newName; UI.showToast('✅ تم تحديث الاسم', 'success'); }
            },
            editBio: function() { 
                const newBio = prompt('أدخل السيرة الذاتية الجديدة:', AuthSystem.currentUser.bio);
                if (newBio) {
                    AuthSystem.currentUser.bio = newBio;
                    document.getElementById('profileBio').innerHTML = '<i class="fas fa-info-circle"></i> ' + newBio + '<button class="edit-bio-btn" onclick="ProfileSystem.editBio()"><i class="fas fa-pen"></i></button>';
                    UI.showToast('✅ تم تحديث السيرة', 'success');
                }
            },
            changeAvatar: function() {
                const input = document.createElement('input');
                input.type = 'file';
                input.accept = 'image/*';
                input.onchange = (e) => {
                    const file = e.target.files[0];
                    if (!file) return;
                    const reader = new FileReader();
                    reader.onload = (e) => {
                        const newAvatar = e.target.result;
                        AuthSystem.currentUser.avatar = newAvatar;
                        document.querySelectorAll('.user-avatar, .profile-avatar-large, .post-modal-avatar, #profileAvatar, #currentUserAvatar, #modalUserAvatar').forEach(el => {
                            if (el) {
                                el.style.backgroundImage = `url(${newAvatar})`;
                                el.style.backgroundSize = 'cover';
                                el.style.backgroundPosition = 'center';
                                el.textContent = '';
                            }
                        });
                        localStorage.setItem('novalex_user_avatar', newAvatar);
                        UI.showToast('✅ تم تغيير الصورة الشخصية', 'success');
                    };
                    reader.readAsDataURL(file);
                };
                input.click();
            },
            editAvatar: function() { this.changeAvatar(); },
            togglePrivacy: function() { 
                const statusEl = document.getElementById('accountStatus');
                if (statusEl.classList.contains('public')) {
                    statusEl.className = 'status-badge private';
                    statusEl.innerHTML = '<i class="fas fa-lock"></i> خاص';
                } else {
                    statusEl.className = 'status-badge public';
                    statusEl.innerHTML = '<i class="fas fa-globe"></i> عام';
                }
                UI.showToast('🔒 تم تغيير حالة الحساب', 'success');
            },
            switchTab: function(tab) { 
                document.querySelectorAll('.profile-tab').forEach(t => t.classList.remove('active'));
                event.currentTarget.classList.add('active');
                UI.showToast(`عرض ${tab}`, 'info'); 
            },
            showSaved: function() { UI.showToast('📌 المحفوظات', 'info'); }
        };

        const Settings = {
            openSettings: function() { Navigation.switchPage('settings'); },
            toggleDarkMode: function() { 
                document.body.classList.toggle('dark-mode'); 
                UI.showToast(document.body.classList.contains('dark-mode') ? '🌙 الوضع المظلم' : '☀️ الوضع الفاتح', 'info');
            },
            openFullSettings: function() { this.openSettings(); },
            openPrivacy: function() { UI.showToast('🔒 إعدادات الخصوصية', 'info'); },
            changeLanguage: function() { UI.showToast('🌐 تغيير اللغة - قريباً', 'info'); }
        };

        function checkIfAdmin() {
            if (AuthSystem.currentUser.email === 'test@novalex.com') document.getElementById('adminSection').style.display = 'block';
            else document.getElementById('adminSection').style.display = 'none';
        }

        function loadSavedAvatar() {
            const saved = localStorage.getItem('novalex_user_avatar');
            if (saved) {
                AuthSystem.currentUser.avatar = saved;
                document.querySelectorAll('.user-avatar, .profile-avatar-large, .post-modal-avatar, #profileAvatar, #currentUserAvatar, #modalUserAvatar').forEach(el => {
                    if (el) {
                        el.style.backgroundImage = `url(${saved})`;
                        el.style.backgroundSize = 'cover';
                        el.style.backgroundPosition = 'center';
                        el.textContent = '';
                    }
                });
            }
        }

        function makeResponsive() {
            const vh = window.innerHeight * 0.01;
            document.documentElement.style.setProperty('--vh', `${vh}px`);
        }

        window.addEventListener('load', function() {
            makeResponsive();
            window.addEventListener('resize', makeResponsive);
            ReportsManager.loadReports();
            loadSavedAvatar();
            if (WalletSystem) WalletSystem.init(); // تهيئة المحفظة
            if (AuthSystem?.init) AuthSystem.init();
            if (Stories?.loadStories) Stories.loadStories();
            if (LiveSystem?.loadLiveStreams) LiveSystem.loadLiveStreams();
            if (MarketSystem?.init) MarketSystem.init();
            if (FeedSystem?.loadFeed) FeedSystem.loadFeed();
            if (ChatSystem?.init) ChatSystem.init();
            if (NotificationSystem?.render) NotificationSystem.render();
            console.log('✅ المنصة جاهزة مع جميع الميزات');
        });
    </script>
</body>
</html>
