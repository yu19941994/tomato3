<template>
  <div class="home">
    <div class="wrap" :class="{'open': isOpen}">
      <!--Sidebar-->
      <div class="sidebarbox">
        <div class="sidebar">
          <div class="tasklist">
             <!--addNew-->
            <div class="addNew" v-if="menu === 'addNew'">
              <p class="font font-h3">ADD NEW TASK</p>
              <hr>
              <p class="font font-h5 totaltitle">TASK TITLE</p>
              <form>
                <label for=""></label>
                <input type="text" placeholder="Input Your Task Name" v-model="newTodoTemp.title" @keyup.enter="addTodo">
              </form>
              <p class="font font-h5 totaltitle">ESTIMATE TOMATO</p>
              <div class="tomato-count" v-for="index in setting.maxTimer" :key="index" @click="newTodoTemp.timerTotal = index">
                <img v-if="newTodoTemp.timerTotal >= index" src="@/assets/stylesheets/image/colorTomato.png" class="tomato-single">
                <img v-else src="@/assets/stylesheets/image/noneTomato.png" class="tomato-single">
              </div>
              <div class="btn btn-primary" @click="addTodo">ADD TASK</div>
            </div>
            <!--list-->
            <div class="list" v-if="menu === 'list'">
              <p class="font font-h3">TASK LISTS</p>
              <hr>
              <div class="badge-group">
                <div class="badge badge-secondary-light"
                :class="{'active':visibility === 'todo'}"
                @click.prevent="visibility = 'todo'">TODO</div>
                <div class="badge badge-secondary-light"
                :class="{'active':visibility === 'done'}"
                @click.prevent="visibility = 'done'">DONE</div>
              </div>
              <ul>
                <li v-for="todo in filterTodos" :key="todo.id" @click="chooseTodo(todo)">
                  <div class="listTitle">
                    <div>
                      <i class="far fa-check-circle"></i> 
                      <p class="font font-h4">{{todo.title}}</p>
                    </div>
                    <i class="fas fa-ellipsis-h" @click="editTodos(todo)"></i>
                  </div>
                  <div v-if="todo.id === editingTodos.id">
                    <div class="listContent">
                    <p class="font font-h5 totaltitle">TASK TITLE</p>
                    <form>
                      <label for=""></label>
                      <input type="text"
                      v-model="editingTitle" @keyup.enter="saveTodos(todo)">
                    </form>
                    <p class="font font-h5 totaltitle">ESTIMATE TOMATO</p>
                    <div class="tomato-count" v-for="index in todo.timerTotal" :key="index">
                      <img src="@/assets/stylesheets/image/colorTomato.png" class="tomato-single">
                      <!-- <img src="@/assets/stylesheets/image/noneTomato.png" class="tomato-single"> -->
                    </div>
                    </div>
                    <div class="listBtnGroup">
                      <div class="btn btn-secondary-light" @click="removeTodos(todo.id)">DELETE</div>
                      <div class="btn btn-success-state" @click="doneTodos(todo)">DONE</div>
                      <div class="btn btn-primary" @click="saveTodos(todo)">SAVE</div>
                    </div>
                  </div>
                 
                </li>
              </ul>
            </div>
            <!--graph-->
            <div class="graph" v-if="menu === 'graph'">
              <p class="font font-h3">ANALYTICS REPORT</p>
              <hr>
              <p class="font font-h5 totaltitle">TOMATO OF THIS WEEK</p>
              <div class="total">
                <div class="tomatos">
                  <p class="font font-text">{{weekDateStatusLogArr[0].completedSum}}</p>
                  <p class="font font-h7">TODAY</p>
                </div>
                <div class="weekly">
                  <p class="font font-text">{{weekTotal}}</p>
                  <p class="font font-h7">WEEK</p>
                </div>
              </div>
              <p class="font font-h5 totaltitle">CHART</p>
              <div class="chart">
                <!-- <div class="chart-inside"></div> -->
              </div>
              <div class="day">
                <p class="font font-h7" v-for="(day,index) in weekDateStatusLogArr" :key="index">{{day.shortDateStr}}</p>
              </div>
              <div class="graphBtn">
                <div class="btn-outline btn-outline-oprimary">PREV</div>
                <div class="btn-outline btn-outline-oprimary">NEXT</div>
              </div>
            </div>
            <!--music-->
            <div class="music" v-if="menu === 'music'">
              <p class="font font-h3">RING TONG</p>
              <hr>
              <ul>
                <li>
                  <div class="listTitle">
                    <div>
                      <i class="far fa-check-circle"></i> 
                      <p class="font-h4">Ring tone1</p>
                    </div>
                    <i class="far fa-play-circle"></i>
                  </div>
                  
                </li>
              </ul>
            </div>
          </div>
          <div class="toolbar">
              <ul>
                <li @click="menu = 'addNew'"><i href="#" class="fas fa-plus-circle" @click="isOpen = true"></i></li>
                <li @click="menu = 'list'"><i href="#" class="fas fa-bars" @click="isOpen = true"></i></li>
                <li @click="menu = 'graph'"><i href="#" class="fas fa-chart-bar" @click="isOpen = true"></i></li>
                <li @click="menu = 'music'"><i href="#" class="fas fa-music"  @click="isOpen = true"></i></li>
              </ul>
          </div>
        </div>
      </div>
      <!-- container -->
      <div class="container" v-if="activeTodo">
        <div class="container-inside">
          <h1 class="font-h2">{{activeTodo.title}}</h1>
          <div class="tomato-container">
            <template v-for="index in activeTodo.timerTotal">
              <img v-if="index <= (activeTodo.timerTotal - activeTodo.timerCount)" 
              src="@/assets/stylesheets/image/colorTomato.png" class="tomato-single" :key="index">
              <img v-else src="@/assets/stylesheets/image/noneTomato.png" class="tomato-single" :key="index">
            </template>
          </div>
          <div class="count">
            <div class="clock">
              <svg height="300" weight="300">
                <circle
                  class="bg"
                  cx="150"
                  cy="150"
                  r="125"
                  stroke="#ACACAC"
                  stroke-width="50"
                  fill="#eaeaea"
                />
                <circle
                  class="loading"
                  cx="150"
                  cy="150"
                  r="125"
                  stroke="#ea5548"
                  stroke-width="50"
                  fill="#eaeaea"
                  :stroke-dasharray="circumference"
                  :stroke-dashoffset="progress"
                />
              </svg>
              <!-- <p class="font font-h1" v-if="!isDefault"><span>{{ setting.minutes }}</span>:<span>{{ setting.seconds }}</span></p> -->
              <p class="font font-h1"><span>{{ control.minutes }}</span>:<span>{{ control.seconds }}</span></p>
            </div>
          </div>
          <div class="func-btn">
            <div class="circlebtn circlebtn-white" @click="startTimer">
              <i class="fas fa-play"></i>
            </div>
            <div class="circlebtn circlebtn-white" @click="pauseTimer">
              <i class="fas fa-pause"></i>
            </div>
            <div class="circlebtn circlebtn-white" @click="resetTimer">
              <i class="fas fa-undo-alt"></i>
            </div>
          </div>
          <p class="font font-h5 subtitle">TASK COMPLETE</p>
        </div>
        
      </div>
      <!-- container -->
      <div class="container" v-else>
        <div class="container-inside">
          <h1 class="font-h2">My First Task</h1>
          <div class="tomato-container">
            <img src="@/assets/stylesheets/image/colorTomato.png" class="tomato-single">
          </div>
          <div class="count">
            <div class="clock">
              <svg height="300" weight="300">
                <circle
                  class="bg"
                  cx="150"
                  cy="150"
                  r="125"
                  stroke="#ACACAC"
                  stroke-width="50"
                  fill="#eaeaea"
                />
                <circle
                  class="loading"
                  cx="150"
                  cy="150"
                  r="125"
                  stroke="#ea5548"
                  stroke-width="50"
                  fill="#eaeaea"
                  :stroke-dasharray="circumference"
                  :stroke-dashoffset="progress"
                />
              </svg>
              <p class="font font-h1"><span>{{ control.minutes }}</span>:<span>{{ control.seconds }}</span></p>
            </div>
          </div>
          <div class="func-btn">
            <div class="circlebtn circlebtn-white" @click="startTimer">
              <i class="fas fa-play"></i>
            </div>
            <div class="circlebtn circlebtn-white" @click="stopTimer">
              <i class="fas fa-pause"></i>
            </div>
            <div class="circlebtn circlebtn-white" @click="resetTimer">
              <i class="fas fa-undo-alt"></i>
            </div>
          </div>
          <p class="font font-h5 subtitle">TASK COMPLETE</p>
        </div>
      </div>
      <!--toggleButton-->
      <div class="tomatoIcon" @click="openSidebar">
        <div>
          <img src="@/assets/stylesheets/image/tomato.png" alt="">
          <i class="fas fa-arrow-right" v-if="!isOpen"></i>
          <i class="fas fa-arrow-left" v-if="isOpen"></i>
          <p class="font font-h3 startText">開始番茄鐘</p>
        </div> 
      </div>
      
      <!-- Modal -->
      <div class="modal" v-if="isShow === true">
        <div id="finish-modal" class="modal-overlay">
          <div class="modal-content">
            <div class="modal-header">
              <p class="font font-h2">恭喜</p>
            </div>
            <div class="modal-body">
              <p class="font font-h4">您已完成番茄鐘</p>
            </div>
            <div class="modal-footer" >
              <div class="btn-alert">
                <div class="btn btn-secondary-light" @click="isShow = false">CANCEL</div>
                <div class="btn-lg btn-lg-primary" @click="doneClock(activeTodo)">DONE</div>
              </div>
            </div>
            </div>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as d3 from 'd3'
// import $ from 'jquery'

export default {
  name: 'Home',
  data () {
    return{
      setting: {
        maxTimer: 10,
        // maxTime: 25 * 60,
        maxTime: 10,
        percentage: 100,
      },
      isOpen: false,
      isShow: false,
      noClock: true,
      timer: '',
      count: '',
      menu: 'addTask',
      todos: [],
      visibility: 'todo',
      editingTodos: {},
      editingTitle: '',
      todayTotal: 0,
      weekTotal: 0,
      now: new Date(),
      newTodoTemp: {
        title: '',
        timerTotal: 1
      },
      activeTodoId: '',
      globalId: 0,
      // control 是控制時鐘
      control: {
        status: 'default',
        time: 0,
        minutes: '',
        seconds: '',
        percentage: 100
      },
      weekDateStatusLogArr: []
    }
  },
  watch: {
    async menu (newValue) {
      switch (newValue) {
        case 'graph':
          await this.dayTomatoCount()
          await this.weekTomatoCount()
          this.$nextTick(() => this.getChart())
      }
    }
  },
  methods: {
    openSidebar () {
      this.isOpen = !this.isOpen
    },
    countTime () {
      this.control.time--
      this.control.seconds = this.fixNumber(this.control.time % 60)
      this.control.minutes = this.fixNumber(Math.floor(this.control.time / 60))
      if (this.control.time === 0) {
        this.stopTimer()
        this.activeTodo.timerCount++
        if (this.activeTodo.timerCount < this.activeTodo.timerTotal) {
          this.startTimer()
        }else {
          this.isShow = true
        }
      }
     
    },
    rotate () {
      this.control.percentage = 100 * (this.control.time / this.setting.maxTime)
    },
    pad (n) {
      //如果小於10，給它'0x'
      return (n < 10 ? '0' : '')+n
    },  
    fixNumber (num) {
      let strNum = String(num)
      if (strNum.length < 2) {
        return `0${strNum}`
      }
      return strNum
    },
    startTimer (item) {
      this.control.status = 'running'
      clearInterval(this.timer)
      this.timer = setInterval(() => {
        this.countTime()
        this.rotate()
      }, 1000)
    },
    pauseTimer () {
      clearInterval(this.timer)
      this.control.status = 'pause'
    },
    stopTimer () {
      clearInterval(this.timer)
      this.resetTimer()
      this.control.status = 'stop'
    },
    resetTimer (isBtnTrigger) {
      // 要回到default時鐘
      if (isBtnTrigger) {
        this.pauseTimer()
      }
      clearInterval(this.timer)
      this.control.time = this.setting.maxTime
      this.control.seconds = this.fixNumber(this.control.time % 60)
      this.control.minutes = this.fixNumber(Math.floor(this.control.time / 60))
      this.control.percentage = 100
    },
    addTodo () {
      this.noClock = false
      let title = this.newTodoTemp.title.trim() //裁減前後空白
      if(!title){
        return
      }
      this.todos.push({
        id: this.globalId++,
        title,
        completed: false,
        timerCount: 0,
        timerTotal: this.newTodoTemp.timerTotal,
        date : new Date()
      })
      this.newTodoTemp.title = ''
      this.newTodoTemp.timerTotal = 1
    },
    removeTodos (todo) {
      let newIndex = this.todos.findIndex((item) => {
        return todo.id === item.id
      })
      this.todos.splice(newIndex,1)
    },
    doneTodos (e) {
      this.todos.completed = e.completed
      e.completed = true
    },
    doneClock (e) {
      this.todos.completed = e.completed
      e.completed = true
      this.isShow = false
    },
    editTodos (e) {
      this.editingTodos = e
      this.editingTitle = e.title
    },
    saveTodos (item) {
      item.title = this.editingTitle
      this.editingTodos = {}
    },
    chooseTodo (todo) {
      this.activeTodoId = todo.id
     
    },
    getChart () {
      d3.select('.chart')
        .selectAll(".chart-inside")
        .data(this.weekDateStatusLogArr.map(log => log.completedSum))
        .enter()
        .text(function (d) { return d})
        .append('div')
        .attr('class', 'bar')
        .style('height', (d => { //將data的值取出作為高
          return `${d * 20}px`
        }))
        // .text(function (d) { return d})
        // .append('p', (d => {
        //   return `this.listData`
        // }))
    },
    dayTomatoCount () {
      let getFullDate = d => {
        let date = new Date(d)
        return `${date.getFullYear()}/${date.getMonth() + 1}/${date.getDate()}`
      }
      let tempNowDate = getFullDate(new Date())
      for (let i = 0; i < 7; i++) {
        const timestamp = new Date(new Date().valueOf() + 86400000 * i)
        console.log(timestamp)
        const shortDateStr = `${timestamp.getMonth() + 1}/${timestamp.getDate()}`
        console.log(shortDateStr)
        if (!this.weekDateStatusLogArr[i]) {
          this.weekDateStatusLogArr[i] = {}
        }
        this.weekDateStatusLogArr[i].shortDateStr = shortDateStr
        this.weekDateStatusLogArr[i].completedSum = 0
      }
      
      this.todos.forEach(todo => {
        const todoDate = getFullDate(new Date())
        if (todo.completed){
          //拿今天去減掉過去
          const dueDateNum = Math.floor((new Date(tempNowDate).valueOf() - new Date(todoDate).valueOf()) / 86400000)
          // console.log(dueDateNum)
          if (dueDateNum < 7) {
            this.weekDateStatusLogArr[dueDateNum].completedSum++
          }
          // countAll = countAll + e.timerTotal
        }
      })
      
      // finishTomato = countAll
      // this.listData.push(finishTomato)
    },
    // isSameWeek (){
    //   let countWeek = 0
    //   let oneDayTime = 1000*60*60*24
    //   let date = new Date()
    //   this.todos.forEach(e => {
    //     if(parseInt((parseInt(e.date.getTime()/oneDayTime)+4)/7) === parseInt((parseInt(date.getTime()/oneDayTime)+4)/7)){
    //       countWeek++   
    //     }
    //   })
    //   this.weekTotal = countWeek
    // }
    weekTomatoCount () {
      this.weekTotal = this.weekDateStatusLogArr.reduce((accumulator, currentValue) => {
        return accumulator + currentValue.completedSum
      }, 0)
    }
  },
  components: {},
  computed: {
    circumference () {
      return 125*2*Math.PI
    },
    progress () {
      return this.circumference - this.circumference*this.setting.percentage/100
    },
    filterTodos () {
      let newTodos = []
      if(this.visibility === 'todo'){
        this.todos.forEach(item=>{
          if(!item.completed){
            newTodos.push(item)
          }
        })
      }else if(this.visibility === 'done'){
        this.todos.forEach(item=>{
          if(item.completed){
            newTodos.push(item)
          }
        })
      }
      return newTodos
    },
    activeTodo () {
      return this.todos.find(todo => todo.id === this.activeTodoId)
    }
  },
  created () {
    this.resetTimer()
    // this.todayTomatoCount()
  }
}
</script>
<style lang="scss" scoped>
.sidebarbox .sidebar .tasklist  .chart {
  min-height: 250px;
  display: flex;
  align-items: flex-end;
}
.sidebarbox .sidebar .tasklist .chart .bar {

}
</style>
