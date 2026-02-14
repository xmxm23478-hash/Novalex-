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
</html>
