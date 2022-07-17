<script setup>
import { reactive, watch } from 'vue'
import QrcodeVue from 'qrcode.vue'
// import html2canvas from 'html2canvas'
const data = reactive({
  qrCodeValue: '',
  faultTolerance: 'H',
  size: 1000,
  phone: '',
  wifi: {
    name: '',
    password: '',
    type: 'nopass'
  },
  message: {
    phone: '',
    content: ''
  },
  contact: {
    name: '',
    phone: '',
    homePhone: '',
    fax: '',
    mail: '',
    personalProfile: '',
    company: '',
    position: '',
    workPhone: '',
    workAddress: '',
    workFax: '',
    website: ''
  }
})
const exportQrCode = () => {
  // 下载二维码
  //下载图片事件,支持Edge,谷歌浏览器,火狐浏览器
  //不支持IE11，IE10
  if (data.qrCodeValue == '') return this.$message.error('This is an error message!')

  const gCanvas = document.querySelector('.qr-code')
  gCanvas.toBlob((blob) => {
    const timestamp = Date.now().toString()
    const a = document.createElement('a')
    document.body.append(a)
    a.download = `${timestamp}.png`
    a.href = URL.createObjectURL(blob)
    a.click()
    a.remove()
  })
}
const changeTabs = () => {
  data.qrCodeValue = data.phone = ''
  clearObjectAttributeValue(data.wifi)
  clearObjectAttributeValue(data.message)
  clearObjectAttributeValue(data.contact)
}

const clearObjectAttributeValue = (obj) => {
  Object.keys(obj).forEach((key) => {
    if (obj[key] != '') {
      if (key == 'type') {
        obj[key] = 'nopass'
      } else {
        obj[key] = ''
      }
    }
  })
  // return tempObj
}

watch(
  () => data.wifi,
  (v) => {
    data.wifi.type = v.password ? 'WPA' : 'nopass'
    data.qrCodeValue = `WIFI:S:${v.name};T:${v.type};P:${v.password};`
  },
  { deep: true }
)
watch(
  () => data.phone,
  (v) => {
    data.qrCodeValue = `TEL:${v}`
  }
)
watch(
  () => data.message,
  (v) => {
    data.qrCodeValue = `SMSTO:${v.phone}:${v.content}`
  },
  { deep: true }
)

watch(
  () => data.contact,
  (v) => {
    let str = ['BEGIN:VCARD', 'VERSION:3.0']
    if (v.website) str.push(`URL:${v.website}`)
    if (v.name) str.push(`N:${v.name}`)
    if (v.homePhone) str.push(`TEL;HOME;VOICE:${v.homePhone}`)
    if (v.phone) str.push(`TEL;CELL;VOICE:${v.phone}`)
    if (v.mail) str.push(`EMAIL:${v.mail}`)
    if (v.personalProfile) str.push(`NOTE:${v.personalProfile}`)
    if (v.fax) str.push(`TEL;HOME;FAX:${v.fax}`)
    if (v.company) str.push(` ORG:${v.company}`)
    if (v.position) str.push(`TITLE:${v.position}`)
    if (v.workPhone) str.push(`TEL;WORK;VOICE:${v.workPhone}`)
    if (v.workAddress) str.push(`ADR;WORK;POSTAL:${v.workAddress}`)
    if (v.workFax) str.push(`TEL;WORK;FAX:${v.workFax}`)
    str.push(`END:VCARD`)
    data.qrCodeValue = str.join('\n')
  },
  { deep: true }
)
</script>

<template>
  <div class="left-box">
    <a-tabs direction="vertical" type="line" :animation="true" @change="changeTabs">
      <a-tab-pane key="1">
        <template #title>
          <span class="iconfont icon-text" title="文本"></span>
        </template>
        <a-textarea v-model="data.qrCodeValue" placeholder="请输入文本内容" allow-clear />
      </a-tab-pane>
      <a-tab-pane key="2">
        <template #title>
          <span class="iconfont icon-URLguanli" title="网址"></span>
        </template>
        <a-input
          v-model="data.qrCodeValue"
          :style="{ width: '320px' }"
          placeholder="http://"
          allow-clear
        >
          <template #prepend>网址</template>
        </a-input>
      </a-tab-pane>
      <a-tab-pane key="3">
        <template #title>
          <span class="iconfont icon-shouye" title="联系人"></span>
        </template>
        <div class="contact-box">
          <div>
            <a-input v-model="data.contact.name" :style="{ width: '320px' }" allow-clear>
              <template #prepend>姓名</template>
            </a-input>
            <a-input v-model="data.contact.phone" :style="{ width: '320px' }" allow-clear>
              <template #prepend>手机</template>
            </a-input>
            <a-input v-model="data.contact.homePhone" :style="{ width: '320px' }" allow-clear>
              <template #prepend>家庭电话</template>
            </a-input>
            <a-input v-model="data.contact.fax" :style="{ width: '320px' }" allow-clear>
              <template #prepend>传真</template>
            </a-input>
            <a-input v-model="data.contact.mail" :style="{ width: '320px' }" allow-clear>
              <template #prepend>邮箱</template>
            </a-input>
            <a-input v-model="data.contact.personalProfile" :style="{ width: '320px' }" allow-clear>
              <template #prepend>个人简介</template>
            </a-input>
          </div>
          <div>
            <a-input v-model="data.contact.company" :style="{ width: '320px' }" allow-clear>
              <template #prepend>公司</template>
            </a-input>
            <a-input v-model="data.contact.position" :style="{ width: '320px' }" allow-clear>
              <template #prepend>职位</template>
            </a-input>
            <a-input v-model="data.contact.workPhone" :style="{ width: '320px' }" allow-clear>
              <template #prepend>工作电话</template>
            </a-input>
            <a-input v-model="data.contact.workAddress" :style="{ width: '320px' }" allow-clear>
              <template #prepend>工作地址</template>
            </a-input>
            <a-input v-model="data.contact.workFax" :style="{ width: '320px' }" allow-clear>
              <template #prepend>工作传真</template>
            </a-input>
            <a-input v-model="data.contact.website" :style="{ width: '320px' }" allow-clear>
              <template #prepend>网址</template>
            </a-input>
          </div>
        </div>
      </a-tab-pane>
      <a-tab-pane key="4">
        <template #title>
          <span class="iconfont icon-shoujihao" title="手机号"></span>
        </template>
        <a-input v-model="data.phone" :style="{ width: '320px' }" allow-clear>
          <template #prepend>电话号码</template>
        </a-input>
      </a-tab-pane>
      <a-tab-pane key="5">
        <template #title>
          <span class="iconfont icon-duanxin" title="短信"></span>
        </template>
        <a-input v-model="data.message.phone" :style="{ width: '320px' }" allow-clear>
          <template #prepend>电话号码</template>
        </a-input>
        <a-textarea
          v-model="data.message.content"
          class="message-textarea"
          placeholder="请输入短信内容"
          allow-clear
        />
      </a-tab-pane>
      <a-tab-pane key="6">
        <template #title>
          <span class="iconfont icon-wifi" title="WIFI"></span>
        </template>
        <div class="wifi-box">
          <a-input v-model="data.wifi.name" :style="{ width: '320px' }" allow-clear>
            <template #prepend>WIFI</template>
          </a-input>
          <a-input v-model="data.wifi.password" :style="{ width: '320px' }" allow-clear>
            <template #prepend>密码</template>
          </a-input>
          <div>
            <span class="arco-input-prepend">加密</span>
            <a-select v-model="data.wifi.type" default-value="nopass" :style="{ width: '100%' }">
              <a-option value="WPA">WPA/WPA2</a-option>
              <a-option value="WEP">WEP</a-option>
              <a-option value="nopass">无加密</a-option>
            </a-select>
          </div>
        </div>
      </a-tab-pane>
    </a-tabs>
  </div>
  <div class="right-box">
    <qrcode-vue
      :value="data.qrCodeValue"
      :size="data.size"
      :level="data.faultTolerance"
      class="qr-code"
    />
    <div class="select-box">
      <div>
        <span
          >容错
          <a-popover position="right">
            <icon-question-circle />
            <template #content> 容错率越高，二维码可遮挡部分越多 </template>
          </a-popover></span
        >
        <a-select
          v-model="data.faultTolerance"
          :style="{ width: '100%' }"
          :trigger-props="{ autoFitPopupMinWidth: true }"
        >
          <a-option value="L">最低(可遮7%)</a-option>
          <a-option value="M">低(可遮15%)</a-option>
          <a-option value="Q">中等(可遮25%)</a-option>
          <a-option value="H">高(可遮30%)</a-option>
        </a-select>
      </div>
      <div>
        <span>尺寸</span>
        <a-select
          v-model="data.size"
          :style="{ width: '100%' }"
          :trigger-props="{ autoFitPopupMinWidth: true }"
        >
          <a-option :value="200">200×200 像素</a-option>
          <a-option :value="400">400×400 像素</a-option>
          <a-option :value="800">800×800 像素</a-option>
          <a-option :value="1000">1000×1000 像素</a-option>
        </a-select>
      </div>
      <div>
        <a-button type="primary" shape="round" @click="exportQrCode">
          <template #icon>
            <icon-export />
          </template>
          下载图片
        </a-button>
      </div>
    </div>
  </div>
</template>

<style lang="less">
@import '../assets/css/iconfont.css';

#app {
  display: flex;
}
.wifi-box {
  display: flex;
  flex-direction: column;
  div {
    display: flex;
    width: 30vw !important;
    margin-top: 10px;
  }
}
.arco-input-outer {
  margin: 10px 0;
  width: 30vw !important;
}

.select-box {
  width: 30vw;
}
.select-box > div {
  display: flex;
  flex-direction: row;
  justify-content: center;
  margin-top: 20px;
}
.select-box > div > span {
  width: 70px;
  margin-right: 5px;
  display: flex;
  align-items: center;
  display: flex;
  justify-content: space-between;
  .arco-icon-question-circle {
    color: #999;
  }
}

.qr-code {
  width: 25vw !important;
  height: 25vw !important;
  margin-top: 50px;
  padding: 15px;
  box-shadow: 0 0 6px rgb(0 0 0 / 5%);
}

.left-box {
  width: 60%;
}

.right-box {
  display: flex;
  align-items: center;
  flex-direction: column;
  width: 40%;
  margin-left: 18px;
  margin-right: 18px;
  font-size: 15px;
}

.arco-textarea:nth-of-type(1) {
  height: 98vh !important;
}
.message-textarea {
  .arco-textarea {
    height: 86vh !important;
  }
}
.contact-box {
  height: 98vh;
  // background: #cccccc;
  overflow: hidden;
  overflow-y: scroll;
}
/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/
::-webkit-scrollbar {
  width: 7px;
  height: 7px;
  background-color: #f5f5f5;
}
/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
  border-radius: 10px;
  background-color: #f5f5f5;
}
/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb {
  border-radius: 10px;
  box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
  -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.1);
  background-color: #c8c8c8;
}
</style>
