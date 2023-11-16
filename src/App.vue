<template>
  <div>
    <button @click="download">download</button>
    <canvas
      ref="canvas"
      @mousedown="handleMouseDown"
      @mousemove="handleMouseMove"
      @mouseup="handleMouseUp"
      @mouseout="handleMouseOut"
      :width="500"
      :height="500"
    ></canvas>
  </div>
</template>

<script lang="js">
export default {
  data() {
    return {
      dragging: false,
      position: { x: 0, y: 0 },
      lastPos: { x: 0, y: 0 },
      image: new Image(),
    };
  },

  methods: {
    async download() {
      const image = await fetch(this.image.src)
      const imageBlog = await image.blob()
      const imageURL = URL.createObjectURL(imageBlog)

      const link = document.createElement('a')
      link.href = imageURL
      link.download = 'image file name here'
      document.body.appendChild(link)
      link.click()
      document.body.removeChild(link)
    },
    drawImage() {
      const context = this.$refs.canvas.getContext('2d');
      context.clearRect(0, 0, this.$refs.canvas.width, this.$refs.canvas.height);
      context.drawImage(this.image, this.position.x, this.position.y);
    },

    handleMouseDown(event) {
      const rect = this.$refs.canvas.getBoundingClientRect();

      const x = event.clientX - rect.left;
      const y = event.clientY - rect.top;

      if (x >= this.position.x && x <= this.position.x + this.image.width &&
          y >= this.position.y && y <= this.position.y + this.image.height) {
        this.dragging = true;
        this.lastPos = { x: event.clientX, y: event.clientY };
      }
    },

    handleMouseMove(event) {
      if (!this.dragging) return;

      const dx = event.clientX - this.lastPos.x;
      const dy = event.clientY - this.lastPos.y;

      this.position.x += dx;
      this.position.y += dy;
      this.lastPos = { x: event.clientX, y: event.clientY };

      this.drawImage();
    },

    handleMouseUp() {
      this.dragging = false;
    },

    handleMouseOut() {
      this.dragging = false;
    },
  },

  mounted() {
    this.image.src = 'https://picsum.photos/200'; // Remplacer ce texte par l'URL de votre image
    this.image.onload = () => {
      this.drawImage();
    };
  },
};
</script>