<template>
  <div>
    <div class="spent-amount">
      Потрачено: 
      <div class="spent-marker"></div>  
      {{ spentAmount }}
    </div>
    <div class="remaining-amount">
      Осталось: 
      <div class="remaining-marker"></div>  
      {{ remainingAmount }}
    </div>
    <canvas id="myCanvas" width="200" height="200"></canvas>
  </div>
</template>

<script>
export default {
  props: {
    radianPercent: {
      type: Number,
      required: true
    },
    spentAmount: {
      type: Number,
      required: true
    },
    remainingAmount: {
      type: Number,
      required: true
    }
  },
  mounted() {
    this.drawCircle();
  },
  methods: {
    drawCircle() {
      const canvas = document.getElementById('myCanvas');
      if (!canvas) {
        console.error('Canvas element not found');
        return;
      }
      const ctx = canvas.getContext('2d');
      if (!ctx) {
        console.error('Canvas context not supported');
        return;
      }
      
      const centerX = canvas.width / 2;
      const centerY = canvas.height / 2;
      const radius = canvas.width / 2 - 10; // Оставляем небольшой отступ для тени и обводки
      const startAngle = 0;
      const endAngle = Math.PI * 2;
      const counterClockwise = false;

      const gradientRemaining = ctx.createRadialGradient(centerX, centerY, radius * 0.4, centerX, centerY, radius);
      gradientRemaining.addColorStop(0, 'lightblue');
      gradientRemaining.addColorStop(1, 'blue');

      ctx.beginPath();
      ctx.arc(centerX, centerY, radius, startAngle, endAngle, counterClockwise);
      ctx.fillStyle = gradientRemaining;
      ctx.shadowBlur = 10;
      ctx.shadowColor = 'rgba(0, 0, 0, 0.3)';
      ctx.fill();

      const gradientSpent = ctx.createRadialGradient(centerX, centerY, radius * 0.4, centerX, centerY, radius);
      gradientSpent.addColorStop(0, 'lightblue');
      gradientSpent.addColorStop(1, 'red');

      ctx.beginPath();
      ctx.moveTo(centerX, centerY);
      ctx.arc(centerX, centerY, radius, startAngle, endAngle * this.radianPercent, counterClockwise);
      ctx.fillStyle = gradientSpent;
      ctx.shadowBlur = 10;
      ctx.shadowColor = 'rgba(0, 0, 0, 0.3)';
      ctx.fill();

      ctx.beginPath();
      ctx.arc(centerX, centerY, radius, startAngle, endAngle, counterClockwise);
      ctx.lineWidth = 2;
      ctx.strokeStyle = 'rgba(0, 0, 0, 0.1)';
      ctx.stroke();
    }
  }
};
</script>

<style scoped>
.spent-amount,
.remaining-amount {
  display: flex;
  align-items: center;
}

.spent-marker,
.remaining-marker {
  width: 10px;
  height: 10px;
  margin-right: 5px;
  border-radius: 50%;
}

.spent-marker {
  background-color: red;
}

.remaining-marker {
  background-color: blue;
}
</style>
