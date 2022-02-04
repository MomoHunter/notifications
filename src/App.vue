<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <div class="flex">
    <button @click="initNotif()">
      Ask for notifications
    </button>
    <button @click="registerPeriodicNewsCheck()">
      Add periodic sync notifications
    </button>
    <div>{{ text }}</div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data () {
    return {
      text: ''
    }
  },
  created () {
    if (navigator.serviceWorker) {
      navigator.serviceWorker.onmessage = (event) => {
        if (event.data) {
          switch (event.data.type) {
            case 'newUpdate':
              this.text = 'hier neue Update'
              break
            case 'updateFinished':
              this.text = 'update finish'
              break
            default:
              console.log(event.data.content)
          }
        }
      }
    }
  },
  methods: {
    initNotif () {
      if (!("Notification" in window)) {
        alert("This browser does not support desktop notification")
      }

      // Let's check whether notification permissions have already been granted
      else if (Notification.permission === "granted") {
        // If it's okay let's create a notification
        // var notification = new Notification("Hi there!")
        navigator.serviceWorker.ready.then(function(registration) {
          registration.showNotification('Vibration Sample', {
            body: 'Buzz! Buzz!',
            tag: 'sample'
          })
        })
      }

      // Otherwise, we need to ask the user for permission
      else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function (permission) {
          // If the user accepts, let's create a notification
          if (permission === "granted") {
            // var notification = new Notification("Hi there!")
            navigator.serviceWorker.controller.postMessage({
              type: 'message',
              content: 'Haha, it works'
            })
          }
        });
      }
    },
    async registerPeriodicNewsCheck() {
      const status = await navigator.permissions.query({
        name: 'periodic-background-sync',
      });
      if (status.state === 'granted') {
        console.log('Periodic background sync can be used.')
      } else {
        console.log('Periodic background sync cannot be used.')
      }
      const registration = await navigator.serviceWorker.ready;
      try {
        console.log('await registration')
        await registration.periodicSync.register('get-test-info', {
          minInterval: 5 * 60 * 1000
        });
        console.log('registration successful')
      } catch (error) {
        console.error('Periodic Sync could not be registered!', error);
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
