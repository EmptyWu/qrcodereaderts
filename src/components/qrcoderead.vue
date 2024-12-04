<script setup lang="ts">
import { ref,reactive } from 'vue'
import { QrcodeStream } from 'vue-qrcode-reader';
import type { DetectedBarcode, BarcodeFormat } from 'barcode-detector/pure'
const paused = ref(false);
const showScanConfirmation = ref(false);
const result = ref('');
const errrormsg=ref('');
const rawValue=ref('');
const rData=reactive({
    SMCPONumber:'',                 // SMC PO NO
    UPCCode:'',                     // SMC UPC Code
    SMCPN:'',                       // SMC PN
    Qty:0,
    LotNo:'',
    DateCode:'',
    Manufacturer:'',                // MFGR
    ManufacturerPartNumber:'',      //MFG PN
    InnerPackageID:'',               // PKG ID
    MadeIn:'',
    FW:''
})

const onCameraOff = () => {
    showScanConfirmation.value = true;
}
const onCameraOn = () => {
    showScanConfirmation.value = false;
}
const onDetect = async (detectedCodes: DetectedBarcode[]) => {
    result.value = JSON.stringify(detectedCodes.map((code) => code.rawValue))
    const rawData = result.value.replace('[','').replace(']','').replace('\n','').trim(); // 去除換行符號
    rawValue.value=rawData;
    const rawDataArray = rawData.split(':');
    
    rData.SMCPONumber = rawDataArray[1];            // 5000074226
    rData.UPCCode = rawDataArray[2];                // N/A
    rData.SMCPN = rawDataArray[3];                  // HDS-MUN-MTFDKCC3T8TGP1BK
    rData.Qty = parseInt(rawDataArray[4], 10);      // 30
    rData.LotNo = rawDataArray[5];                  // HZ001JF.5B
    rData.DateCode = rawDataArray[6];               // 2432
    rData.Manufacturer = rawDataArray[7];           // MICRON
    rData.ManufacturerPartNumber = rawDataArray[8]; // MTFDKCC3T8TGP-1BK1JABYY
    rData.InnerPackageID = rawDataArray[9];         // MS0004T03-24817-4717
    rData.MadeIn = rawDataArray[10];                // Malaysia
    rData.FW = rawDataArray[11];                    // E3MQ000

    paused.value = true
    await timeout(500)
    paused.value = false
}

const clickbtn = () =>{
    result.value ="[\"SL:5000074226:N/A:HDS-MUN-MTFDKCC3T8TGP1BK:30:HZ001JF.5B:2432:MICRON:MTFDKCC3T8TGP-1BK1JABYY:MS0004T03-24817-4717:Malaysia:E3MQ000\n\"]";
    const rawData = result.value.replace('[','').replace(']','').replace('\n','').trim(); // 去除換行符號
   
    const rawDataArray = rawData.split(':');
    
    rData.SMCPONumber = rawDataArray[1];            // 5000074226
    rData.UPCCode = rawDataArray[2];                // N/A
    rData.SMCPN = rawDataArray[3];                  // HDS-MUN-MTFDKCC3T8TGP1BK
    rData.Qty = parseInt(rawDataArray[4], 10);      // 30
    rData.LotNo = rawDataArray[5];                  // HZ001JF.5B
    rData.DateCode = rawDataArray[6];               // 2432
    rData.Manufacturer = rawDataArray[7];           // MICRON
    rData.ManufacturerPartNumber = rawDataArray[8]; // MTFDKCC3T8TGP-1BK1JABYY
    rData.InnerPackageID = rawDataArray[9];         // MS0004T03-24817-4717
    rData.MadeIn = rawDataArray[10];                // Malaysia
    rData.FW = rawDataArray[11];                    // E3MQ000
}

const timeout = (ms:number) => {
    return new Promise((resolve) => {
        window.setTimeout(resolve, ms)
    })
};
const onError=()=>{
    errrormsg.value=console.error.toString();
}
</script>

<template>
 <div> 
        <qrcode-stream :paused="paused" @detect="onDetect" @camera-on="onCameraOn" @camera-off="onCameraOff"
            @error="onError">
            <div v-show="showScanConfirmation" class="scan-confirmation">
                <img src='/checkmark.svg' alt="Checkmark" width="128" />
            </div>
        </qrcode-stream>
        <p class="decode-result">
            Last result: <b>{{ result }}</b><br/>

            SMC PO NO: <b>{{rData.SMCPONumber}}</b><br />            
            Vendor Code: <b>{{rData.InnerPackageID?.split('-')[0]}}</b><br />
            SMC UPC Code: <b>{{rData.UPCCode}}</b><br />
            SMC PN: <b>{{rData.SMCPN}}</b><br />
            Qty: <b>{{rData.Qty}}</b><br />
            Lot No: <b>{{rData.LotNo}}</b><br />
            Date Code: <b>{{rData.DateCode}}</b><br />
            MFGR: <b>{{rData.Manufacturer}}</b><br />
            MFG PN: <b>{{rData.ManufacturerPartNumber}}</b><br />
            PKG ID: <b>{{rData.InnerPackageID}}</b><br />
            Made In: <b>{{rData.MadeIn}}</b><br />
            FW: <b>{{rData.FW}}</b><br />

            error : <b>{{ errrormsg }}</b>
        </p>
        <p>{{ rawValue }}</p>
        <p>{{ rData }}</p>
        <button type="button" @click="clickbtn"> 測試</button>
       
    </div>
</template>

<style scoped>
.scan-confirmation {
    position: absolute;
    width: 100%;
    height: 100%;

    background-color: rgba(255, 255, 255, 0.8);

    display: flex;
    flex-flow: row nowrap;
    justify-content: center;
}
</style>