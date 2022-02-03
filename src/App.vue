<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <div class="flex">
    <button @click="initNotif()">
      Ask for notifications
    </button>
  </div>
</template>

<script>
export default {
  name: 'App',
  methods: {
    initNotif () {
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification")
      }

      // Let's check whether notification permissions have already been granted
      else if (Notification.permission === "granted") {
        // If it's okay let's create a notification
        var notification = new Notification("Hi there!")
      }

      // Otherwise, we need to ask the user for permission
      else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function (permission) {
          // If the user accepts, let's create a notification
          if (permission === "granted") {
            var notification = new Notification("Hi there!")
          }
        });
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.flex {
  display: flex;
  flex-direction: column;
}
</style>
