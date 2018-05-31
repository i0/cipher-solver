<template lang="pug">
  div.container
    .pt-3.mb-5
      h5 Message
      div
        b-form-textarea(cols="50", rows="5", autofocus, v-model="message")
    .my-5
      h5
        span Message Rotated by
        span
          input.form-control.encryption-key.mx-2(type="number", v-model="encryptionKeyInput")
        span :
      span.encrypted-message.p-2.my-1 {{ encryptedMessage }}

    .my-5
      h5 Message Frequency Table
      div
        table.table.table-striped
          thead
            tr
              th Character
              th Frequency
          tbody
            tr(v-for="chr in Object.entries(frequency).sort((b, a) => {return a[1] - b[1]})")
              td {{ chr[0] }}
              td {{ chr[1] }}
</template>

<script>
  import Logo from '~/components/Logo.vue'

  export default {
    data() {
      return {
        charToCode: {
          'a': 0,
          'b': 1,
          'c': 2,
          'd': 3,
          'e': 4,
          'f': 5,
          'g': 6,
          'h': 7,
          'i': 8,
          'j': 9,
          'k': 10,
          'l': 11,
          'm': 12,
          'n': 13,
          'o': 14,
          'p': 15,
          'q': 16,
          'r': 17,
          's': 18,
          't': 19,
          'u': 20,
          'v': 21,
          'w': 22,
          'x': 23,
          'y': 24,
          'z': 25,
          // ',': 26,
          // '.': 27,
          // '?': 28,
          // ':': 29,
          // '\'': 30,
          // '"': 31,
          // ' ': 32,
        },
        message: 'PHHW PH DIWHU WKH WRJD SDUWB',
        frequency: {},
        encryptionKeyInput: '-3'
      }
    },
    components: {
      Logo
    },
    watch: {
      message(val) {
        this.calculateFrequency(val.toLowerCase());
      }
    },
    mounted() {
      this.calculateFrequency(this.message.toLowerCase());
    },
    methods: {
      calculateFrequency(msg) {
        this.frequency = {};
        msg.split('').forEach((chr) => {
          if (!this.frequency.hasOwnProperty(chr)) {
            this.frequency[chr] = 1;
          } else {
            this.frequency[chr]++;
          }
        });
      }
    },
    computed: {
      codeToChar: function () {
        return this._.invert(this.charToCode)
      },
      encryptionKey: function () {
        if (!this.encryptionKeyInput) {
          return 0;
        }
        return parseInt(this.encryptionKeyInput)
      },
      encryptedMessage: function () {
        let msg = this.message.toLowerCase();
        let encrypted = '';
        let keySpaceSize = this._.size(this.charToCode);
        for (let i = 0; i < msg.length; i++) {
          let chr = msg[i];
          let encryptedChar = `${chr}`;
          if(this.charToCode.hasOwnProperty(chr)) {
            let charCode = this.charToCode[chr];
            let encryptedCode = (charCode + this.encryptionKey) % keySpaceSize;
            if (encryptedCode < 0) {
              encryptedCode = keySpaceSize + encryptedCode;
            }
            encryptedChar = this.codeToChar[encryptedCode];
          }
          encrypted += encryptedChar;
        }
        return encrypted;
      }
    }
  }
</script>

<style lang="scss">
  input.encryption-key {
    width: 100px;
    display: inline;
  }

  .encrypted-message {
    background: #eee;
    color: black;
    font-style: italic;
    font-size: 20px;
    display: inline;
    letter-spacing: 3px;
    word-break: break-all;
  }
</style>
