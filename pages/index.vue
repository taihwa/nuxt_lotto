<template>
    <div>
        <b-jumbotron header-level="5">
            <template #header style="font-size:10px">자동추첨하기</template>
            <template #lead>
                로또 번호를 추첨하기전 '1등 되고 시펑~!'을 간절히 3번 복창하고 추첨해 보세요. 누가 알아요 하늘이 도와줄지
            </template>
            <hr class="my-4">

            <b-button v-b-modal.modal-1 @click="getTotalNum()" variant="primary" href="#" class="mr-1">전체 번호 추첨</b-button>
            <b-button v-b-modal.modal-2 variant="success" @click="getStatic()" href="#">많이 나온 번호 추첨</b-button>
        </b-jumbotron>
        <b-modal id="modal-1" title="전체 번호 추첨" class="align-middle">
            <lottie :options="lottieOptions" v-on:animCreated="handleAnimation" />
            <div style="text-align:center">
                <b-avatar v-for="(num, index) in totalLottoNum" :key="'D' + index"  :variant="numberVariants[index]" :text="num.toString()" class="mr-1" :class="'fade_in'+index"></b-avatar>
            </div>
            <input type="text" id="hiddenTotalNum" style="position:absolute;left:-1000px;top:-1000px" :value="totalLottoNum" />
            <div class="mt-3">
              <b-button block variant="info" @click="copyLottoNum()">복사하기</b-button>
            </div>
        </b-modal>
        <b-modal id="modal-2" title="많이 나온 번호 추첨" class="align-middle">
            <b-button class="mb-3" block v-b-toggle.static-collapse @click="initCustomNum()">많이 나온 번호 보기</b-button>
            <b-collapse id="static-collapse">
                <b-table id="static-table"
                    striped
                    hover
                    :items="staticManyNums"
                    :fields="fields"
                    :sort-by.sync="sortBy"
                    :sort-desc.sync="sortDesc"
                    :per-page="perPage"
                    :current-page="currentPage"
                    class="text-center"
                    >
                    <template #cell(number)="row">
                       <b-avatar variant="success" :text="row.item.number.toString()"></b-avatar>
                    </template>
                    <template #cell(counts)="row">
                       {{ row.item.counts }}번
                    </template>
                    </b-table>
                <b-pagination
                    v-model="currentPage"
                    pills
                    :total-rows="rows"
                    :per-page="perPage"
                    align="center"
                    aria-controls="static-table"
                ></b-pagination>
            </b-collapse>
            <b-button block variant="info" @click="customAuto(10)">많이 나온 번호 10개로 추첨</b-button>
            <div class="mt-3 text-center">
                <b-avatar v-for="(num, index) in limit10" :key="'A' + index"  :variant="numberVariants[index]" :text="num.toString()" class="mr-1" :class="aniState ? 'fade_in' + index : ''"></b-avatar>
            </div>
            <b-button class="mt-3" block variant="info" @click="customAuto(20)">많이 나온 번호 20개로 추첨</b-button>
            <div class="mt-3 text-center">
              <b-avatar v-for="(num, index) in limit20" :key="'B' + index"  :variant="numberVariants[index]" :text="num.toString()" class="mr-1"></b-avatar>
            </div>
            <b-button class="mt-3" block variant="info" @click="customAuto(30)">많이 나온 번호 30개로 추첨</b-button>
            <div class="mt-3 text-center">
                <b-avatar v-for="(num, index) in limit30" :key="'C' + index"  :variant="numberVariants[index]" :text="num.toString()" class="mr-1"></b-avatar>
            </div>
            
        </b-modal>
    </div>
</template>
<script>
import lottie from 'vue-lottie/src/lottie.vue'
import * as animationData from "~/assets/twinkle.json";
import axios from 'axios'

export default {
  components: {
    lottie
},
  data: () => ({
      anim: null, // for saving the reference to the animation
      lottieOptions: { animationData: animationData.default, loop:true, playSpeed:0.1 },
      aniState: false,
      sortBy: 'counts',
      sortDesc: true,
      selectedNumber: [],
      numberVariants: ['primary', 'info', 'success', 'danger', 'warning', 'secondary'],
      staticManyNums: [],
      totalNumbers: [],
      limit10: [],
      limit20: [],
      limit30: [],
      totalLottoNum: [],
      fields: [
          {
              key: 'number',
              label: '번호',
              sortable: true
          },
          {
              key: 'counts',
              label: '출현 횟수',
              sortable: true
          },
      ],
      perPage: 10,
      currentPage: 1,
  }),
  computed: {
    rows() {
        return this.staticManyNums.length
    }
},
  methods: {
    async numToServer(data) {
        let result = await axios.post('http://lottoapi.tenpm.kr/save_num', data)
        console.log(result)
    },
    copyLottoNum() {
        let copyText = document.querySelector('#hiddenTotalNum');
       
       copyText.select()
       copyText.setSelectionRange(0,99999)
       console.log(copyText)
      try {
          let success = document.execCommand('copy')
          alert('복사 송공')
      } catch (e) {
          alert('복사 실패')
      }
    },
      handleAnimation: function (anim) {
        this.anim = anim;
        console.log(this.anim)
        },
      initCustomNum() {
          this.limit10 = []
          this.limit20 = []
          this.limit30 = []
      },
      getCompleteNum(numbers) {
          // 로또 배열에다가 번호를 1부터 45까지 numbers에 넣는다.
          let selectedNumber = []
          for (let i = 0; i < 6; i++) {
            let lottoNumbers = this.getLottoNum(numbers, selectedNumber, i)
            selectedNumber.push(lottoNumbers)
          }
          return selectedNumber
      },
      getTotalNum() {
          let totalNumbers = []
          for(let i = 1; i < 46; i++) {
            totalNumbers[i-1] = i
          }
        this.totalLottoNum = this.getCompleteNum(totalNumbers)
        this.numToServer(this.totalLottoNum)
      },
      getLottoNum(numbers, selectedNumber, index) {
        let lottoNum = numbers[Math.floor(Math.random() * numbers.length)]
        let check;
        console.log(index)
        if (index > 0) {
            // 첫번째꺼는 넣고 두번째꺼 추첨
            check = selectedNumber.find(num => num == lottoNum)
            console.log(check)
            if (check === undefined) {
                console.log(`중복되지 않은 로또 번호 : ${lottoNum}`)
                return lottoNum
            } else {
                console.log(`중복된 로또 번호 : ${lottoNum}`)
                return this.getLottoNum(numbers, selectedNumber, index)
            }
        } else {
            return lottoNum
        }

      },
      async getStatic() {
          this.staticManyNums = []
          let { data } = await axios.get('http://lottoapi.tenpm.kr/static')
          let statics = data[0]
          for (let i = 0; i < statics.counts.length; i++) {
              let obj = {'number': i+1, 'counts': statics.counts[i]}
              this.staticManyNums.push(obj)
          }
      },
      customAuto(limit) {
          this.staticManyNums.sort((a,b) => {
              return b.counts - a.counts
          })
          let numbers = []
          for (let i = 0; i < limit; i++) {
              numbers.push(this.staticManyNums[i].number)
          }
          this['limit'+limit] = this.getCompleteNum(numbers)
          this.numToServer(this['limit'+limit])
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