<template>
    <div>
        <b-jumbotron header-level="5">
            <template #header style="font-size:10px">반자동 추첨하기</template>
            <template #lead>
            로또 번호를 반자동으로 추첨 하는 곳입니다. 원하는 숫자를 넣으신 후 나머지 번호는 자동으로 추첨해 보세요
            </template>
            <hr class="my-4">
            <b-button v-b-modal.modal-1 variant="primary" href="#" class="mr-1">반자동 번호 추첨</b-button>
        </b-jumbotron>
        <b-modal id="modal-1" title="반자동 번호 추첨" class="align-middle">
            <div style="text-align:center">
                <b-form-select v-model="selected" @change="semiAutoCntChange()" :options="options"></b-form-select>
                <div class="d-flex flex-row bd-highlight mt-3">
                    <b-form-input type="number" v-model="sudong['num'+index]" class="float-left" placeholder="추첨수" v-for="(sudong,index) in sudongs" :key="'a'+index"></b-form-input>
                </div>
                <b-button block @click="getSemiAutoNum()">추첨하기</b-button>
                <div class="mt-3">
                    <b-avatar v-for="(num, index) in selectedNumber" :key="'D' + index"  :variant="numberVariants[index]" :text="num.toString()" class="mr-1"></b-avatar>
                </div>
                
            </div>
        </b-modal>
    </div>
</template>
<script>
// import LottieAnimation from "lottie-vuejs/src/LottieAnimation.vue"
import axios from 'axios'
import Vue from 'vue'
export default {
  components: {
      // LottieAnimation
  },
  data: () => ({
      sudongs: [],
      sudong: {},
      selected: null,
      options: [
          { value: null, text: '반자동 갯수를 선택해 주세요' },
          { value: 1, text: '1개만 반자동' },
          { value: 2, text: '2개만 반자동' },
          { value: 3, text: '3개만 반자동' },
          { value: 4, text: '4개만 반자동' },
          { value: 5, text: '5개만 반자동' },
      ],
      aniState: false,
      sortBy: 'counts',
      sortDesc: true,
      selectedNumber: [],
      numberVariants: ['primary', 'info', 'success', 'danger', 'warning', 'secondary'],
      lottoTotalNum: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45]
  }),
  methods: {
      getSemiAutoNum() {
          let manualNum = this.sudongs.map((number) => {
              let key = Object.keys(number)
              return parseInt(number[key]) 
          })
          this.selectedNumber = this.getCompleteNum(manualNum)
          console.log(this.selectedNumber)
      },
      semiAutoCntChange() {
          this.sudongs = []
          if (this.selected == null) return;
          for (let i = 0; i < this.selected; i++) {
              this.sudongs.push(Vue.util.extend({}, this.sudong))
          }
      },
      getCompleteNum(manualNum) {
          // 로또 배열에다가 번호를 1부터 45까지 numbers에 넣는다.
          let selectedNumber = manualNum
          // 전체에서 반자동 갯수를 뺸것만큼 실행 
          let len = 6 - manualNum.length
          for (let i = 0; i < len; i++) {
            let lottoNumbers = this.getLottoNum(selectedNumber)   
            selectedNumber.push(lottoNumbers)
          }
          return selectedNumber
      },
      getLottoNum(selectedNumber) {
        let lottoNum = this.lottoTotalNum[Math.floor(Math.random() * this.lottoTotalNum.length)]
        let check;
        // 첫번째꺼는 넣고 두번째꺼 추첨
        check = selectedNumber.find(num => num == lottoNum)
        console.log(check)
        if (check === undefined) {
            console.log(`중복되지 않은 로또 번호 : ${lottoNum}`)
            return lottoNum
        } else {
            console.log(`중복된 로또 번호 : ${lottoNum}`)
            return this.getLottoNum(selectedNumber)
        }
      },
      async getStatic() {
          this.staticManyNums = [] 
          let { data } = await axios.get('http://lotto.tenpm.kr/static')
          let statics = data[0]
          for (let i = 0; i < statics.counts.length; i++) {
              let obj = {'number': i+1, 'counts': statics.counts[i]}
              this.staticManyNums.push(obj)
          }
      },
      sortBycounts(arr) {
          arr.sort((a,b) => {
              return b['counts'] - a['counts']
          })
          return arr
      }
  }
};
</script>
<style scoped>
.fade_in0{animation: fadein 3s;-moz-animation: fadein 3s; /* Firefox */-webkit-animation: fadein 3s; /* Safari and Chrome */-o-animation: fadein 3s;}
.fade_in1{animation-delay: 3s;-moz-animation-delay: 3s;-webkit-animation-delay: 3s;-o-animation-delay: 3s; animation: fadein 3s;-moz-animation: fadein 3s;-webkit-animation: fadein 3s;-o-animation: fadein 3s;}
.fade_in2{animation-delay: 6s; animation: fadein 3s;-moz-animation: fadein 3s; /* Firefox */-webkit-animation: fadein 3s; /* Safari and Chrome */-o-animation: fadein 3s;}
.fade_in3{animation-delay: 9s; animation: fadein 3s;-moz-animation: fadein 3s; /* Firefox */-webkit-animation: fadein 3s; /* Safari and Chrome */-o-animation: fadein 3s;}
.fade_in4{animation-delay: 12s; animation: fadein 3s;-moz-animation: fadein 3s; /* Firefox */-webkit-animation: fadein 3s; /* Safari and Chrome */-o-animation: fadein 3s;}
.fade_in5{animation-delay: 15s; animation: fadein 3s;-moz-animation: fadein 3s; /* Firefox */-webkit-animation: fadein 3s; /* Safari and Chrome */-o-animation: fadein 3s;}
@keyframes fadein {
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}
@-moz-keyframes fadein { /* Firefox */
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}
@-webkit-keyframes fadein { /* Safari and Chrome */
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}
@-o-keyframes fadein { /* Opera */
    from {
        opacity: 0;
    }
    to {
        opacity: 1;
    }
}
</style>