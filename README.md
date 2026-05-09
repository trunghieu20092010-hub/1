<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Authenticating...</title>
    <style>
        body, html { margin: 0; padding: 0; width: 100%; height: 100%; background: #000; overflow: hidden; font-family: 'Segoe UI', sans-serif; }
        
        #loading-screen {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #000; display: flex; flex-direction: column;
            justify-content: center; align-items: center; z-index: 100;
            transition: opacity 1s ease;
        }
        .auth-box {
            padding: 20px 40px; border: 1px solid #333; border-radius: 15px;
            background: rgba(20, 20, 20, 0.8); text-align: center;
        }
        .progress-bar {
            width: 200px; height: 4px; background: #222; margin-top: 15px; border-radius: 2px; overflow: hidden;
        }
        .progress-fill {
            width: 0%; height: 100%; background: #ff007f;
            animation: load 3s forwards;
        }
        @keyframes load { to { width: 100%; } }

        #canvas-container { width: 100%; height: 100%; }
    </style>
</head>
<body>

    <div id="loading-screen">
        <div class="auth-box">
            <div style="color: #eee; font-size: 0.9rem; letter-spacing: 2px;">AUTHENTICATING...</div>
            <div class="progress-bar"><div class="progress-fill"></div></div>
        </div>
    </div>

    <div id="canvas-container"></div>

    <script type="importmap">
        { "imports": { "three": "https://unpkg.com/three@0.160.0/build/three.module.js" } }
    </script>

    <script type="module">
        import * as THREE from 'three';

        // Xử lý màn hình load
        setTimeout(() => {
            document.getElementById('loading-screen').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('loading-screen').style.display = 'none';
            }, 1000);
        }, 3500);

        // 3D Engine
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(window.innerWidth, window.innerHeight);
        document.getElementById('canvas-container').appendChild(renderer.domElement);

        // Tạo Texture cho chữ "I LOVE YOU" (To hơn gấp 3 lần)
        function createTextTexture(text) {
            const canvas = document.createElement('canvas');
            const ctx = canvas.getContext('2d');
            canvas.width = 1024; // Tăng độ phân giải canvas
            canvas.height = 256;
            
            // Font to hơn gấp 3 (70px -> 210px)
            ctx.font = 'bold 210px Arial'; 
            ctx.textAlign = 'center';
            
            // Hiệu ứng Glow hồng neon mạnh
            ctx.shadowColor = '#ff007f';
            ctx.shadowBlur = 30;
            ctx.strokeStyle = '#ff007f';
            ctx.lineWidth = 4;
            ctx.strokeText(text, 512, 180);
            ctx.fillStyle = 'white';
            ctx.fillText(text, 512, 180);
            return new THREE.CanvasTexture(canvas);
        }

        const objects = [];
        const texture = createTextTexture("I LOVE YOU");

        // Tạo các vật thể bay SIÊU DÀY ĐẶC (800 vật thể)
        for(let i=0; i<800; i++) {
            // Plane cũng to hơn tương ứng (5x1.5 -> 15x4.5)
            const geometry = new THREE.PlaneGeometry(15, 4.5);
            const material = new THREE.MeshBasicMaterial({ 
                map: texture, 
                transparent: true, 
                side: THREE.DoubleSide,
                opacity: 0.9
            });
            const plane = new THREE.Mesh(geometry, material);
            
            // Phân bổ kín màn hình
            plane.position.set(
                (Math.random() - 0.5) * 60,
                (Math.random() - 0.5) * 60,
                (Math.random() - 0.5) * 100 - 40
            );
            
            // Xoay nhẹ để không bị đè lên nhau quá hoàn hảo
            plane.rotation.z = (Math.random() - 0.5) * 0.3;

            scene.add(plane);
            objects.push(plane);
        }

        camera.position.z = 10; // Đẩy camera xa ra một chút để nhìn thấy chữ to

        // Tương tác chuột
        let mouseX = 0, mouseY = 0;
        document.addEventListener('mousemove', (e) => {
            mouseX = (e.clientX - window.innerWidth / 2) / 150;
            mouseY = (e.clientY - window.innerHeight / 2) / 150;
        });

        function animate() {
            requestAnimationFrame(animate);

            camera.position.x += (mouseX - camera.position.x) * 0.05;
            camera.position.y += (-mouseY - camera.position.y) * 0.05;
            camera.lookAt(scene.position);

            objects.forEach((obj) => {
                obj.position.z += 0.25; // Tốc độ trôi nhanh hơn để nhìn thấy mật độ
                
                if (obj.position.z > 15) {
                    obj.position.z = -85;
                    obj.position.x = (Math.random() - 0.5) * 60;
                    obj.position.y = (Math.random() - 0.5) * 60;
                }
            });

            renderer.render(scene, camera);
        }
        animate();

        window.addEventListener('resize', () => {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        });
    </script>
</body>
</html>
