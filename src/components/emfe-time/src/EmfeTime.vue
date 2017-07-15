<template>
  <div class="emfe-time" v-emfe-documentclick="close" :class="timeName">
    <button class="emfe-time-btn" v-if="!open" @click="toggle">
      <span class="emfe-time-btn-text" :class="{'emfe-time-btn-text-choice': choiced}">{{ time }}</span>
      <!-- 日期 -->
      <emfe-icon type="hint" className="emfe-time" v-show="!choiced" @icon-click="toggle"></emfe-icon>
      <!-- 取消 -->
      <emfe-icon type="qr" className="emfe-time" v-show="choiced" @icon-click="cancel"></emfe-icon>
    </button>
    <emfe-transition name="fade">
      <div class="emfe-time-box" v-show="status" :class="{'emfe-time-box-position': !open}">
        <div class="emfe-time-main">
          <div class="emfe-time-item">
            <ul class="emfe-time-list">
              <li class="emfe-time-list-item" v-for="hourLoop in hours" :class="{'emfe-time-list-item-on': hourLoop.num === hour, 'emfe-time-list-item-disable': hourLoop.undo}" @click.stop="choiceHour(hourLoop)">{{ hourLoop.num }}</li>
            </ul>
          </div>
          <div class="emfe-time-item">
            <ul class="emfe-time-list">
              <li class="emfe-time-list-item" v-for="minuteLoop in minutes" :class="{'emfe-time-list-item-on': minuteLoop.num === minute, 'emfe-time-list-item-disable': minuteLoop.undo}" @click.stop="choiceMinute(minuteLoop)">{{ minuteLoop.num }}</li>
            </ul>
          </div>
          <div class="emfe-time-item">
            <ul class="emfe-time-list">
              <li class="emfe-time-list-item" v-for="(secondLoop, secondIndex) in seconds" :class="{'emfe-time-list-item-on': secondLoop.num === second, 'emfe-time-list-item-disable': secondLoop.undo}" @click.stop="choiceSecond(secondLoop)">{{ secondLoop.num }}</li>
            </ul>
          </div>
        </div>
        <div v-if="confirm" class="emfe-time-footer">
          <button class="emfe-time-ok" @click.stop="ok">确定</button>
        </div>
      </div>
    </emfe-transition>
  </div>
</template>
<script>
import TimeTool from '../../../tools/time';

const hourNum = 24;
const minuteNum = 60;
const secondNum = 60;
const zero = '00';

const handleConputedTime = (i, times) => {
  const newTimes = {
    num: TimeTool.zeroFill(i),
    undo: false,
  };
  if (times.length > 0) {
    times.every((time) => {
      const newTime = TimeTool.zeroFill(time);
      newTimes.undo = newTime === newTimes.num;
      return !newTimes.undo;
    });
  }
  return newTimes;
};

export default {
  name: 'EmfeTime',
  data() {
    return {
      hours: [],
      minutes: [],
      seconds: [],
      hour: '',
      minute: '',
      second: '',
      choiced: false,
      status: this.open,
    };
  },
  props: {
    format: {
      type: String,
      default: '/',
    },
    // 默认文案
    placeholder: {
      type: String,
      default: '选择时间',
    },
    confirm: {
      type: Boolean,
      default: true,
    },
    // 默认显示
    open: {
      type: Boolean,
      default: false,
    },
    // 禁用
    disabledHours: {
      type: Array,
      default: () => [],
    },
    // 禁用
    disabledMinutes: {
      type: Array,
      default: () => [],
    },
    // 禁用
    disabledSeconds: {
      type: Array,
      default: () => [],
    },
    className: String,
  },
  computed: {
    timeName() {
      return [
        {
          [`${this.className}-time`]: !!this.className,
        },
      ];
    },
    time() {
      let time = this.placeholder;
      if (this.choiced) {
        time = `${this.hour}:${this.minute}:${this.second}`;
      }
      return time;
    },
  },
  mounted() {
    for (let i = 0; i < hourNum; i++) {
      this.hours.push(handleConputedTime(i, this.disabledHours));
    }
    for (let i = 0; i < minuteNum; i++) {
      this.minutes.push(handleConputedTime(i, this.disabledMinutes));
    }
    for (let i = 0; i < secondNum; i++) {
      this.seconds.push(handleConputedTime(i, this.disabledSeconds));
    }
  },
  methods: {
    setChoice() {
      if (!this.choiced) {
        this.hour = zero;
        this.minute = zero;
        this.second = zero;
        this.choiced = true;
      }
    },
    choiceHour(hour) {
      if (!hour.undo) {
        this.setChoice();
        this.hour = hour.num;
        this.$emit('choice', this.time);
      }
    },
    choiceMinute(minute) {
      if (!minute.undo) {
        this.setChoice();
        this.minute = minute.num;
        this.$emit('choice', this.time);
      }
    },
    choiceSecond(second) {
      if (!second.undo) {
        this.setChoice();
        this.second = second.num;
        this.$emit('choice', this.time);
      }
    },
    toggle() {
      this.status = !this.status;
    },
    close(e, noClose) {
      if (!this.open) {
        if (!noClose && this.status) {
          this.$emit('close', this.date);
        }
        this.status = false;
      }
    },
    ok() {
      this.close(true);
      this.$emit('ok', this.time);
    },
    cancel() {
      this.choiced = false;
      this.hour = '';
      this.minute = '';
      this.second = '';
      this.$emit('cancel', this.time);
    },
  },
};
</script>