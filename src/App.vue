<template>
  <div id="app">
    <template v-for="activity in activities">
      <input type="radio"
        :id="activity"
        :value="activity"
        name="activity"
        v-model="activity_status">
      <label :for="activity">{{ activity }}</label>
    </template>
    <div class="speech_list"
      v-for="speech in current_speeches">
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
        <div v-if='date_now == speech.speech_date '
          class='speech_status comming_status'>
          <span>即將開始</span>
        </div>
        <div v-else-if='date_now < speech.speech_date'
          class='speech_status this_week_status'>
          <span>本週活動</span>
        </div>
        <div v-else='speech.speech_date < date_now'
          class='speech_status past_status'>
          <span>已經結束</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'app',
  data () {
    return {
      activities: ['all_speeches', 'comming_speeches', 'past_speeches' ],
      activity_status: 'all_speeches',
      current_speeches: null,
      all_speeches: null,
      comming_speeches: null,
      past_speeches: null,
      date_now: "2018-03-22"
    }
  },
  mounted: function () {
    this.get_data()
  },
  methods:{
    get_data: function () {
      var vm = this
      axios.get('https://devche.com/api/speech/data')
      .then(function (response) {
        // console.log(response);
        response.data.result.sort(function(a, b) {
          var nameA = a.speech_date;
          var nameB = b.speech_date;
          if (nameA < nameB) {
            return 1;
          }
          if (nameA > nameB) {
            return -1;
          }
          return 0;
        });
        vm.current_speeches = response.data.result
        vm.all_speeches = response.data.result
        vm.comming_speeches = vm.all_speeches.filter(speech => speech.speech_date > vm.date_now)
        vm.past_speeches = vm.all_speeches.filter(speech => speech.speech_date < vm.date_now)

      })
      .catch(function (error) {
        console.log(error)
      });
    },
    show_speeche: function(){
      switch (this.activity_status) {
        case "all_speeches": this.current_speeches = this.all_speeches
          break;
        case "comming_speeches": this.current_speeches = this.comming_speeches
          break;
        case "past_speeches": this.current_speeches = this.past_speeches
          break;
        default:
      }
    }
  },
  watch: {
    activity_status: 'show_speeche'
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
  .comming_status{
    background-color: #5d9c6c;
  }
  .this_week_status{
    background-color: #1e8ba6;;
  }
  .past_status{
    background-color: #7a062e;
  }

}


</style>
