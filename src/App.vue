<template>
  <div id="app">
    <template v-for="condition in conditions">
      <input type="radio"
        :id="condition"
        :value="condition"
        name="condition"
        v-model="filter_condition">
      <label :for="condition">{{ translate_condition(condition) }}</label>
    </template>
    <div class="speech_list"
      v-for="speech in filter_speech()">
      <div class="speech_block">
        <div class="class_img">
          <img :src="speech.class_img" class="class_img" alt="">
        </div>
        <div class="speech_content">
          <h3 class="speech_title">{{speech.title}}</h3>
          <p class="speech_time">{{speech.speech_date}}</p>
          <div class="inner_content">
            <div class="left_content">
              <div class="speaker_photo">
                <img :src="speech.speaker_img" class="speaker_photo" alt="">
              </div>
              <span class="speaker_name">{{speech.speaker}}</span>
            </div>
            <div class="right_content">
              <p class="speech_introduce">{{speech.message}}</p>
            </div>
          </div>
        </div>
        <div class="speech_status" :class="speech.status">
          {{speech_status_info[speech.status]}}
        </div>
      </div>
    </div>
  </div>
</template>

<script src="moment.js"></script>
<script>
import axios from 'axios';
import * as moment from 'moment'
export default {
  name: 'app',
  data () {
    return {
      conditions: ['all', 'today', 'past' ],
      speech_status_info:{
        comming: '本週活動',
        today: '即將開始',
        past: '已經結束'
      },
      filter_condition: 'all',
      all_speechs: null,
      date_now: moment().format('YYYY-MM-DD'),
      date_now: '2018-03-22' //Demo 用
    }
  },
  mounted: function () {
    this.get_speech()
  },
  methods:{
    get_speech: function () {
      var vm = this
      axios.get('https://devche.com/api/speech/data')
      .then(function (response) {
        response.data.result
          .sort(function(a, b) {
            var nameA = a.speech_date;
            var nameB = b.speech_date;
            if (nameA < nameB) {
              return 1;
            }
            if (nameA > nameB) {
              return -1;
            }
            return 0;
          })
          .map(item => {
            item.status = (function(){
              if(vm.date_now < item.speech_date) return 'comming'
              if(vm.date_now == item.speech_date) return 'today'
              if(item.speech_date < vm.date_now) return 'past'
            })()
          })
        vm.all_speechs = response.data.result
      })
      .catch(function (error) {
        console.log(error)
      });
    },
    filter_speech: function(){
      console.log('filter_speech');
      if(this.filter_condition == 'all'){
        return this.all_speechs
      }
      return this.all_speechs.filter(speech =>
        speech.status === this.filter_condition
      )
    },
    translate_condition: function (condition){
      switch (condition) {
        case 'all': return '全部活動'
          break;
        case 'today': return '即將開始活動'
          break;
        case 'past': return '已經結束活動'
          break;
        default:
      }
    }
  },
}
</script>

<style lang="scss">
*{
  // box-shadow: 0 0 1px red;
}
h3, p, span{
  margin-top: 0;
  margin-bottom: 5px;
}

#app{
  max-width: 1000px;
  margin-left: auto;
  margin-right: auto;
  font-family: Avenir,Helvetica,Arial,sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

}

.speech_block{
  display: flex;
  min-height: 140px;
  margin-bottom: 12px;
  .class_img{
    width: 140px;
    height: 140px;
    margin-right: 10px;
  }
  .speech_content{
    padding: 10px;
    flex: 1;
  	background-color: #1e8ba61a;
    .inner_content{
      display: flex;
    }
    .left_content{
      width: 210px;
      margin-right: 20px;
    }
    .right_content{
      flex: 1;
    }
    .speech_title{
      font-size: 20px;
      font-weight: 500;
      color: #7a062e;
    }
    .speech_time{
    	font-size: 18px;
    	font-weight: 500;
      color: #117f7e;
    }
    .speaker_name{
      line-height: 50px;
      margin-left: 10px;
      font-size: 20px;
      font-weight: 500;
      color: #0f375b;
    }
    .speech_introduce{
      font-size: 16px;
      color: #0f375b;
      overflow : auto;
      height: 46px;
       // text-overflow: ellipsis;
       // display: -webkit-box;
       // -webkit-line-clamp: 2;
       // -webkit-box-orient: vertical;
    }
    .speaker_photo{
      -webkit-clip-path: circle(50% at 50% 50%);
      float: left;
      background-color: #fff;
      width: 50px;
      height: 50px;
    }
  }
  .speech_status{
    width: 40px;
    text-align: center;
    line-height: 40px;
    font-size: 18px;
  	font-weight: 500;
    -webkit-writing-mode: vertical-rl;
    color: #fff;
  }
  .today{
    background-color: #5d9c6c;
  }
  .comming{
    background-color: #1e8ba6;;
  }
  .past{
    background-color: #7a062e;
  }

}


</style>
