# Flover
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flower Message 🌸</title>
    <script src="https://cdn.jsdelivr.net/npm/@tailwindcss/browser@4"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Itim&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Itim', cursive; }
        @keyframes flowerDrop {
            0% { transform: translateY(-50px) scale(0); opacity: 0; }
            50% { transform: translateY(20px) scale(1.2); opacity: 1; }
            100% { transform: translateY(0) scale(1); opacity: 1; }
        }
        .animate-drop { animation: flowerDrop 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }
    </style>
</head>
<body class="bg-pink-50 min-h-screen flex flex-col items-center justify-center p-4 text-gray-700">

    <div id="creator-view" class="w-full max-w-md bg-white rounded-3xl p-6 shadow-xl border-4 border-pink-200 hidden">
        <div id="step-1">
            <h1 class="text-2xl text-center text-pink-500 font-bold mb-4">🌸 สร้างกระถางดอกไม้ของคุณ 🌸</h1>
            <div class="mb-4">
                <label class="block text-sm font-bold text-gray-500 mb-1">หัวข้อกระถาง:</label>
                <input type="text" id="pot-title" placeholder="เช่น สุขสันต์วันเกิดนะ..." class="w-full p-3 border-2 border-pink-100 rounded-2xl focus:outline-none focus:border-pink-300">
            </div>
            
            <div id="message-container" class="space-y-3">
                </div>

            <button id="btn-add-box" class="w-full mt-3 bg-yellow-100 hover:bg-yellow-200 text-yellow-700 font-bold py-2 px-4 rounded-2xl border-2 border-dashed border-yellow-300 transition-all cursor-pointer">
                ➕ เพิ่มกล่องข้อความ (สูงสุด 5)
            </button>
            <button id="btn-next" class="w-full mt-4 bg-pink-400 hover:bg-pink-500 text-white font-bold py-3 px-4 rounded-2xl shadow-md transition-all cursor-pointer">
                ถัดไป ➡️
            </button>
        </div>

        <div id="step-2" class="hidden text-center">
            <h1 id="preview-title" class="text-2xl text-pink-500 font-bold mb-4">ลองรดน้ำต้นไม้ดูสิ!</h1>
            
            <div id="btn-preview-trigger" class="text-8xl my-6 cursor-pointer transform hover:scale-110 transition-transform select-none">
                🪴
            </div>
            <p class="text-sm text-gray-400 mb-4">(คลิกที่กระถางเพื่อทดลองเปิด)</p>

            <div id="preview-flowers" class="flex flex-wrap justify-center gap-4 my-4"></div>

            <div class="space-y-2 mt-6">
                <button id="btn-copy-link" class="w-full bg-green-400 hover:bg-green-500 text-white font-bold py-3 px-4 rounded-2xl shadow-md transition-all cursor-pointer">
                    🔗 คัดลอกลิงก์เพื่อส่งต่อ
                </button>
                <button id="btn-back" class="w-full bg-gray-200 hover:bg-gray-300 text-gray-600 py-2 px-4 rounded-2xl transition-all cursor-pointer">
                    ย้อนกลับไปแก้ไข
                </button>
            </div>
        </div>
    </div>


    <div id="receiver-view" class="w-full max-w-md bg-white rounded-3xl p-6 shadow-xl border-4 border-teal-100 text-center hidden">
        <h1 id="user-title" class="text-2xl text-teal-600 font-bold mb-2">มีคนส่งของขวัญให้คุณ</h1>
        <p class="text-gray-400 text-sm mb-6">👇 คลิกที่กระถางต้นไม้เพื่อรับดอกไม้และข้อความ 👇</p>
        
        <div id="btn-user-trigger" class="text-9xl my-8 cursor-pointer transform hover:scale-110 transition-transform active:scale-95 select-none">
            🪴
        </div>

        <div id="user-flowers" class="flex flex-col items-center gap-4 mt-6"></div>
    </div>

    <script>
        const flowerEmojis = ['🌸', '🌷', '🌹', '🌻', '🌼'];
        let messages = ['']; // เริ่มต้นด้วย 1 ข้อความ

        // ตรวจสอบพารามิเตอร์ใน URL ว่ามีข้อมูลส่งมาไหม
        const urlParams = new URLSearchParams(window.location.search);
        const encodedData = urlParams.get('d');

        if (encodedData) {
            // ถ้ามีข้อมูลใน URL -> แสดงหน้าต่างคนใช้
            document.getElementById('receiver-view').classList.remove('hidden');
            setupReceiverView(encodedData);
        } else {
            // ถ้าไม่มีข้อมูลใน URL -> แสดงหน้าต่างคนพิมพ์
            document.getElementById('creator-view').classList.remove('hidden');
            setupCreatorView();
        }

        // --- การจัดการฝั่งคนพิมพ์ (Creator) ---
        function setupCreatorView() {
            renderMessageBoxes();

            document.getElementById('btn-add-box').addEventListener('click', () => {
                if (messages.length < 5) {
                    messages.push('');
                    renderMessageBoxes();
                } else {
                    alert('ใส่ได้สูงสุด 5 ดอกจ้า 🌸');
                }
            });

            document.getElementById('btn-next').addEventListener('click', () => {
                const title = document.getElementById('pot-title').value.trim() || "ข้อความจากใจ";
                // ดึงค่าอัปเดตจากช่องพิมพ์ล่าสุด
                messages = Array.from(document.querySelectorAll('.msg-input')).map(input => input.value.trim()).filter(v => v !== '');
                
                if (messages.length === 0) {
                    alert('ช่วยพิมพ์ข้อความอย่างน้อย 1 กล่องนะจ๊ะ!');
                    return;
                }

                document.getElementById('step-1').classList.add('hidden');
                document.getElementById('step-2').classList.remove('hidden');
                document.getElementById('preview-title').innerText = title;
                
                // รีเซ็ตโซนพรีวิว
                document.getElementById('preview-flowers').innerHTML = '';
            });

            document.getElementById('btn-back').addEventListener('click', () => {
                document.getElementById('step-2').classList.add('hidden');
                document.getElementById('step-1').classList.remove('hidden');
            });

            // กดปุ่มทดสอบในหน้าพรีวิว
            document.getElementById('btn-preview-trigger').addEventListener('click', () => {
                triggerFlowerPopUp(messages, document.getElementById('preview-flowers'));
            });

            // กดปุ่มคัดลอกลิงก์
            document.getElementById('btn-copy-link').addEventListener('click', () => {
                const title = document.getElementById('pot-title').value.trim() || "ข้อความจากใจ";
                const dataObj = { t: title, m: messages };
                // แปลงข้อมูลเป็นภาษาไทยที่ปลอดภัยใน URL ด้วย Base64
                const b64 = btoa(encodeURIComponent(JSON.stringify(dataObj)));
                const shareUrl = `${window.location.origin}${window.location.pathname}?d=${b64}`;

                navigator.clipboard.writeText(shareUrl).then(() => {
                    alert('คัดลอกลิงก์เรียบร้อย! ส่งให้เพื่อนได้เลย 🎁');
                }).catch(err => {
                    alert('เกิดข้อผิดพลาดในการก๊อปปี้ กรุณาก๊อปปี้จากช่อง URL โดยตรง');
                });
            });
        }

        function renderMessageBoxes() {
            const container = document.getElementById('message-container');
            container.innerHTML = '';
            messages.forEach((msg, index) => {
                const box = document.createElement('div');
                box.className = "bg-pink-50 p-4 rounded-2xl border-2 border-pink-100 relative";
                box.innerHTML = `
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-sm font-bold text-pink-400">ดอกไม้ที่ ${index + 1} (${flowerEmojis[index]})</span>
                        ${index > 0 ? `<button onclick="removeBox(${index})" class="text-xs text-red-400 hover:text-red-600 cursor-pointer">ลบ</button>` : ''}
                    </div>
                    <textarea maxlength="300" placeholder="พิมพ์ความรู้สึกตรงนี้ได้เลย... (ไม่เกิน 300 ตัวอักษร)" class="msg-input w-full p-2 bg-white rounded-xl border border-pink-200 focus:outline-none focus:border-pink-300 h-20 resize-none text-sm">${msg}</textarea>
                `;
                container.appendChild(box);
            });
        }

        window.removeBox = function(index) {
            // ดึงค่าล่าสุดเก็บไว้ก่อนลบ
            messages = Array.from(document.querySelectorAll('.msg-input')).map(input => input.value);
            messages.splice(index, 1);
            renderMessageBoxes();
        }

        // --- การจัดการฝั่งคนรับ (Receiver) ---
        function setupReceiverView(encodedStr) {
            try {
                const decodedData = JSON.parse(decodeURIComponent(atob(encodedStr)));
                document.getElementById('user-title').innerText = decodedData.t;

                document.getElementById('btn-user-trigger').addEventListener('click', () => {
                    triggerFlowerPopUp(decodedData.m, document.getElementById('user-flowers'), true);
                }, { once: true }); // ให้กดได้ครั้งเดียวเพื่อความลุ้นข้อมูล
            } catch (e) {
                document.getElementById('user-title').innerText = "ลิงก์ไม่ถูกต้องหรือข้อมูลเสียหาย 😢";
            }
        }

        // --- ฟังก์ชันร่วมสำหรับการทำแอนิเมชันป๊อบอัพดอกไม้ ---
        function triggerFlowerPopUp(msgArray, targetContainer, isVertical = false) {
            targetContainer.innerHTML = ''; // เคลียร์ของเก่า
            
            msgArray.forEach((text, index) => {
                setTimeout(() => {
                    const flowerWrapper = document.createElement('div');
                    // ปรับสไตล์ตามหน้าพรีวิว (แนวนอน) หรือ หน้าคนรับ (แนวตั้งยาวลงมา)
                    flowerWrapper.className = isVertical 
                        ? "w-full bg-yellow-50 p-4 rounded-2xl border-2 border-yellow-200 flex flex-col items-center shadow-sm animate-drop"
                        : "w-28 bg-yellow-50 p-2 rounded-xl border border-yellow-200 flex flex-col items-center animate-drop";

                    flowerWrapper.innerHTML = `
                        <div class="${isVertical ? 'text-5xl' : 'text-3xl'} mb-1">${flowerEmojis[index % flowerEmojis.length]}</div>
                        <div class="text-xs text-gray-600 text-center w-full break-words ${isVertical ? 'text-base font-medium p-2 bg-white rounded-xl mt-2 border border-yellow-100' : ''}">${text}</div>
                    `;
                    targetContainer.appendChild(flowerWrapper);
                }, index * 300); // ดีเลย์การหล่นทีละดอกเพื่อความสวยงาม
            });
        }
    </script>
</body>
</html>