#圣诞节
圣诞树
<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <!-- 适配手机端 -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no"/>
  <title>粒子圣诞树 - 手机可看</title>
  <style>
    html, body {
      margin: 0; 
      padding: 0; 
      overflow: hidden; 
      background: #000; 
      touch-action: none; /* 优化手机触摸体验 */
    }
    #info {
      position: absolute; 
      top: 10px; left: 10px; 
      color: #fff; 
      font-size: 14px;
      font-family: Arial, sans-serif; 
      pointer-events: none; /* 避免遮挡交互 */
    }
  </style>
</head>
<body>
  <div id="info">Merry Christmas</div>
  <!-- 引入Three.js核心库 -->
  <script src="https://cdn.jsdelivr.net/npm/three@0.152.0/build/three.min.js"></script>
  <!-- 可选：轨道控制器（手机触摸可旋转视角） -->
  <script src="https://cdn.jsdelivr.net/npm/three@0.152.0/examples/js/controls/OrbitControls.js"></script>
  
  <script>
    // 1. 初始化场景、相机、渲染器
    const scene = new THREE.Scene();
    const camera = new THREE.PerspectiveCamera(
      75, 
      window.innerWidth / window.innerHeight, 
      0.1, 
      1000
    );
    const renderer = new THREE.WebGLRenderer({ antialias: true });
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.body.appendChild(renderer.domElement);

    // 2. 轨道控制器（手机触摸旋转场景）
    const controls = new THREE.OrbitControls(camera, renderer.domElement);
    controls.enableDamping = true; // 惯性阻尼，更丝滑
    controls.target.set(0, -1, 0); // 聚焦圣诞树底部
    controls.update();

    // 3. 创建粒子圣诞树
    const particleCount = 3000; // 粒子数量（越多越密）
    const geometry = new THREE.BufferGeometry();
    const positions = new Float32Array(particleCount * 3);

    // 生成粒子位置：用极坐标模拟圣诞树轮廓
    for (let i = 0; i < particleCount; i++) {
      const theta = Math.random() * Math.PI; // 垂直角度（0~π）
      const phi = Math.random() * 2 * Math.PI; // 水平角度（0~2π）
      // 半径随theta变化，做出“树”的形状（顶部窄、底部宽）
      const radius = Math.random() * (0.4 + 0.6 * Math.sin(2.5 * theta)); 
      const x = radius * Math.sin(theta) * Math.cos(phi);
      const y = (Math.cos(theta) - 1.2) * 1.5; // 垂直方向偏移，让树更修长
      const z = radius * Math.sin(theta) * Math.sin(phi);
      positions[i * 3] = x;
      positions[i * 3 + 1] = y;
      positions[i * 3 + 2] = z;
    }
    geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));

    // 粒子材质：粉色半透明，适配手机视觉
    const material = new THREE.PointsMaterial({
      color: new THREE.Color(0xff9ac1), // 浅粉色
      size: 2,
      transparent: true,
      opacity: 0.9,
      depthWrite: false, // 解决透明粒子排序问题
      blending: THREE.AdditiveBlending // 叠加混合，更梦幻
    });

    const tree = new THREE.Points(geometry, material);
    scene.add(tree);

    // 4. 星空背景（随机小粒子）
    const starCount = 1500;
    const starGeometry = new THREE.BufferGeometry();
    const starPositions = new Float32Array(starCount * 3);
    for (let i = 0; i < starCount; i++) {
      const x = (Math.random() - 0.5) * 30;
      const y = (Math.random() - 0.5) * 30;
      const z = -Math.random() * 30; // 让星星在相机后方，营造深度
      starPositions[i * 3] = x;
      starPositions[i * 3 + 1] = y;
      starPositions[i * 3 + 2] = z;
    }
    starGeometry.setAttribute('position', new THREE.BufferAttribute(starPositions, 3));
    const starMaterial = new THREE.PointsMaterial({
      color: 0xffffff,
      size: 0.8,
      transparent: true,
      opacity: 0.6,
      depthWrite: false,
      blending: THREE.AdditiveBlending
    });
    const stars = new THREE.Points(starGeometry, starMaterial);
    scene.add(stars);

    // 5. 相机位置与渲染循环
    camera.position.set(0, -2, 8); // 初始视角：从斜上方看树

    function animate() {
      requestAnimationFrame(animate);
      controls.update(); // 轨道控制器更新
      renderer.render(scene, camera);
    }
    animate();

    // 6. 窗口resize适配手机
    window.addEventListener('resize', () => {
      camera.aspect = window.innerWidth / window.innerHeight;
      camera.updateProjectionMatrix();
      renderer.setSize(window.innerWidth, window.innerHeight);
    });
  </script>
</body>
</html>
