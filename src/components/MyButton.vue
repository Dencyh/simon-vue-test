<template>
  <div class="button-container">
    <button
      type="button"
      class="button"
      v-bind:disabled="!enabled"
      v-bind:class="[
        !enabled ? 'button-disabled' : pressed ? 'button-down' : 'button-up',
      ]"
      v-text="buttonText"
      v-on:mousedown="press"
      v-on:mouseup="release"
    ></button>
  </div>
</template>
<script>
export default {
  name: "my-button",
  data() {
    return {};
  },
  methods: {
    press() {
      console.log("press(): pressed = " + this.pressed);
      if (this.enabled && !this.pressed) {
        console.log("BUTTON pressed...");
        this.pressed = true;
        console.log("press(): pressed set to " + this.pressed);
      }
      console.log("press() exit: pressed = " + this.pressed);
    },
    enable() {
      console.log("enabling button...");
      this.enabled = true;
    },
    disable() {
      this.enabled = false;
    },
    release() {
      console.log("release(): pressed = " + this.pressed);
      if (this.enabled && this.pressed) {
        console.log("BUTTON released...");
        this.pressed = false;
      }
      console.log("release() exit: pressed = " + this.pressed);
    },
  },
};
</script>
<style>
button {
  display: inline-block;
  /*border: none;*/
  border-width: 0.3vw;
  border-radius: 0.5vw;
  padding: 0.5vw 1vw;
  margin: 0;
  text-decoration: none;
  background: #0069ed;
  font-family: sans-serif;
  font-size: 1rem;
  cursor: pointer;
  text-align: center;
  /*transition: background 250ms ease-in-out, transform 150ms ease;*/
  -webkit-appearance: none;
  -moz-appearance: none;
}

button:hover {
  background: #dddeee;
}

button:focus {
  outline: none;
  /*outline-offset: -4px;*/
}

button-container {
  width: 12vw;
}

.button-disabled {
  background-color: #eeeeee;
  cursor: edit;
  color: #ffffff;
}

.button-up {
  border-style: outset;
  background-color: lightgray;
  color: grey;
}

.button-down {
  border-style: inset;
  background-color: lightgray;
  font-weight: bold;
  color: green;
}
</style>