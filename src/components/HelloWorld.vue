  <template>
    <div id="app">
      <p class="error">{{ error }}</p>
      <p class="decode-result" style="font-size:50%;">Kết quả là: 
        {{result}}<br>
        <b style="color:#f00;">{{ result }}</b>
      </p>
      <qrcode-stream
        :constraints="{ facingMode: 'environment' }"
        :highlighted="true"  
        @decode="onDecode"
        @detect="onDetect"
        @init="onInit"
      />    
    </div>
  </template>

<script>
import { QrcodeStream } from 'vue-qrcode-reader';

export default {
  components: {
    QrcodeStream,
  },
  data() {
    return {
      result: '',
      error: '', 
    };
  },
  methods: {
    onDetect(detectedCodes) {
      this.result = JSON.stringify(detectedCodes.map((code) => code.rawValue))
    },
    onDecode(decodedResult) {
      this.result = decodedResult[0].rawValue;
    },
    async onInit(promise) {
      try {
        await promise;
        console.log("Camera initialized successfully!");
      } catch (err) {
        console.error("Camera initialization error:", err);
        if (err.name === 'NotAllowedError') {
          this.error = "ERROR: you need to grant camera access permission";
        } else if (err.name === 'NotFoundError') {
          this.error = "ERROR: no camera on this device";
        } else if (err.name === 'NotSupportedError') {
          this.error = "ERROR: secure context required (HTTPS, localhost)";
        } else if (err.name === 'NotReadableError') {
          this.error = "ERROR: is the camera already in use?";
        } else if (err.name === 'OverconstrainedError') {
          this.error = "ERROR: installed cameras are not suitable";
        } else if (err.name === 'StreamApiNotSupportedError') {
          this.error = "ERROR: Stream API is not supported in this browser";
        }
      }
    },
  },
};
</script>
<style scoped>
.error {
  font-weight: bold;
  color: red;
}
.barcode-format-checkbox {
  margin-right: 10px;
  white-space: nowrap;
  display: inline-block;
}
</style>
