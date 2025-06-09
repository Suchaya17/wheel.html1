# wheel.html1
<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>วงล้อลุ้นรางวัล</title>
  <style>
    body {
      font-family: sans-serif;
      text-align: center;
      padding: 40px;
    }
    canvas {
      margin: 20px auto;
      display: block;
    }
    #spinButton {
      padding: 10px 20px;
      font-size: 18px;
      cursor: pointer;
    }
    #result {
      margin-top: 20px;
      font-size: 20px;
      font-weight: bold;
    }
    #remaining {
      margin-top: 30px;
    }
  </style>
</head>
<body>

  <h1>🎉 วงล้อลุ้นรางวัล 🎉</h1>
  <canvas id="wheel" width="400" height="400"></canvas>
  <button id="spinButton">หมุนวงล้อ</button>
  <div id="result"></div>
  <div id="remaining"></div>

  <script>
    const canvas = document.getElementById('wheel');
    const ctx = canvas.getContext('2d');
    const spinButton = document.getElementById('spinButton');
    const resultDiv = document.getElementById('result');
    const remainingDiv = document.getElementById('remaining');

    let rewards = {
      "ปากกา": 20,
      "สมุด": 10,
      "ไฮไลท์": 25,
    };

    const segments = [
      "ปากกา", "สมุด", "ไฮไลท์",
      "ขออภัย", "ขออภัย", "ปากกา",
      "ไฮไลท์", "สมุด"
    ];

    let currentAngle = 0;

    function drawWheel() {
      const total = segments.length;
      const angle = (2 * Math.PI) / total;
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      for (let i = 0; i < total; i++) {
        ctx.beginPath();
        ctx.moveTo(200, 200);
        ctx.fillStyle = i % 2 === 0 ? '#FFD700' : '#FFA500';
        ctx.arc(200, 200, 180, angle * i, angle * (i + 1));
        ctx.fill();
        ctx.save();
        ctx.translate(200, 200);
        ctx.rotate(angle * i + angle / 2);
        ctx.fillStyle = '#000';
        ctx.font = '16px sans-serif';
        ctx.fillText(segments[i], 100, 0);
        ctx.restore();
      }
    }

    function updateRemainingDisplay() {
      remainingDiv.innerHTML = `
        <p>🎁 รางวัลคงเหลือ:</p>
        <ul style="list-style: none; padding: 0;">
          <li>ปากกา: ${rewards["ปากกา"]}</li>
          <li>สมุด: ${rewards["สมุด"]}</li>
          <li>ไฮไลท์: ${rewards["ไฮไลท์"]}</li>
        </ul>
      `;
    }

    function spinWheel() {
      let spins = Math.floor(Math.random() * 3 + 5); // 5–7 รอบ
      const total = segments.length;
      const anglePerSegment = 360 / total;
      const endAngle = currentAngle + (spins * 360) + Math.floor(Math.random() * 360);
      const targetSegment = Math.floor(((endAngle % 360) / anglePerSegment)) % total;

      let interval = setInterval(() => {
        currentAngle += 15;
        drawWheel();
        canvas.style.transform = `rotate(${currentAngle}deg)`;
      }, 20);

      setTimeout(() => {
        clearInterval(interval);
        const reward = segments[targetSegment];

        if (rewards[reward] > 0) {
          rewards[reward]--;
          resultDiv.textContent = `คุณได้รับ: ${reward}! 🎉`;
        } else if (reward === "ขออภัย" || !rewards[reward]) {
          resultDiv.textContent = "ขออภัย ลองใหม่อีกครั้งนะ!";
        } else {
          resultDiv.textContent = `${reward} หมดแล้ว 😢`;
        }

        // อัปเดตวงล้อ: ถ้ารางวัลหมด เปลี่ยนชื่อ segment เป็น "ขออภัย"
        for (let i = 0; i < segments.length; i++) {
          if (rewards[segments[i]] === 0) {
            segments[i] = "ขออภัย";
          }
        }

        updateRemainingDisplay();
      }, 3000);
    }

    drawWheel();
    updateRemainingDisplay();
    spinButton.addEventListener('click', spinWheel);

  </script>

</body>
</html>
