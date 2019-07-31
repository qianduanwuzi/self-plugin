<style>
  .selectBox{
    height:20px;
    background:rgba(255,255,255,1);
    border:1px solid rgba(220,220,220,1);
    position: relative;
    color: #1D1E20;
    cursor: pointer;
  }

  .selectBox .showBox{
      padding-left: 6px;
      height: 20px;
  }

  .selectBox .showBox .arrow{
    width:14px;
    height:14px;
    background:rgba(206,119,53,1);
    border-radius:2px;
    float: right;
    margin-right: 2px;
    margin-top: 2px;
    text-align: center;
    line-height: 14px;
  }

  .selectBox .showBox .arrow img{
      width: 12px;
  }

  .selectBox .optsBox{
      position: absolute;
      top: 20px;
      border: 1px solid rgba(220,220,220,1);
      background:rgba(255,255,255,1);
      width: 100%;
      z-index: 1000;
  }

  .selectBox .optsBox .eachOpt{
      text-align: center;
      border-top: 1px solid rgba(220,220,220,.8);
  }

   .selectBox .optsBox .eachOpt:hover{
       color: #fff;
       background:rgba(206,119,53,1);
   }


</style>

<template id="selfSelect">
    <div :class="['selectBox', timestamp]" :style="{width: widths+'px'}" >
        <div :class="['showBox', timestamp]" @click="clickDown">
        <!-- <input type="text" readonly  :placeholder="placeholder"  :value = 'value'> -->
            {{curVal}}
            <div class="arrow" >
                <img src="/images/chosjian.png" alt="" v-show="!showOpt" @click.stop="clickDown">
                <img src="/images/chosjianx.png" alt="" v-show="showOpt" @click.stop="clickDown">
            </div>
        </div>
        <div class="optsBox" v-show="showOpt" >
            <div v-for="(one, index) in opts" :key="index" class="eachOpt" @click="clickHandler(one)">
                {{one[param.key]}}
            </div>
        </div>
    </div>
</template>

<script>
    var SelfSelect = Vue.extend({
        template: "#selfSelect",
        props: {
            widths: Number,
            opts: Array,
            // 默认
            // defaultkey: {},
            // 指定
            param:  {
                default: function () {
                    return {value: 'value', key: 'key'}
                },
                type: Object
            },
            value: {}, // 
            placeholder: String,
            disabled: {
                default: false,
                type: Boolean
            }
        },
        data() {
            return {
                timestamp: '',
                timer: 0,
                showOpt: false, // 控制opt框显示
                curVal: '', // 显示值
            }
        },
        computed: {
            // timestamp(){
            //     return String(new Date().getTime())
            // }
        },
        created() {
            this.timestamp = String(new Date().getTime())
            if(this.value)  {
                this.curVal = this.opts.find(one => one[this.param.value] == this.value) && this.opts.find(one => one[this.param.value] == this.value)[this.param.key];
            }
        },
        mounted: function (){
            document.addEventListener('click',(e) => {
               try{
                   if(!(e.target.className.includes(this.timestamp)))   this.showOpt = false
               }catch(error){
                   console.log(error)
               }
                
            })
        },
        methods: {
            clickDown() {
                if(this.disabled) return 
                this.showOpt = !this.showOpt
                if(this.showOpt){
                    document.getElementsByClassName('entry-select-box-1')[0].style.display="none";
                    this.$emit('showselect',false);
                }
                // enterfram 题型组件点击保证下拉不被遮挡
                this.$emit('clickdown')
            },
            clickBox() {
                return
            },
            clickHandler: function(d) {
                this.showOpt = false;
                // this.curVal = d[this.param.key];
                if( d[this.param.key] !=  this.curVal) {
                    this.curVal = d[this.param.key];
                    this.$emit('change', d[this.param.value])
                } 
            }
        },
        watch: {
            curVal: function(v) {
                // console.log('139', (this.opts.find(one => one[this.param.key] == v) && this.opts.find(one => one[this.param.key] == v)[this.param.value]) || '' )
                // console.log('140', String(this.param.value) in (this.opts.find(one => one[this.param.key] == v) || {})? this.opts.find(one => one[this.param.key] == v)[this.param.value] : '')
                // console.log('141', v, this.opts, this.opts.find(one => one[this.param.key] == v))
                this.$emit('input',(this.opts.find(one => one[this.param.key] == v) && this.opts.find(one => one[this.param.key] == v)[this.param.value]) || '');
            },
            value(v, oldv) {
                // console.log('145', v, oldv)
                // 取消操作时保留初始状态
                if(this.timer == 0) {
                    this.$emit('changetopipe', oldv)
                    this.timer += 1
                }
                if(v) {
                    this.curVal = this.opts.find(one => one[this.param.value] == this.value) && this.opts.find(one => one[this.param.value] == this.value)[this.param.key];
                }
            }
            // opts: {
            //     deep: true,
            //     handler(v) {
            //         if(this.value) {
            //             this.curVal = this.opts.find(one => one[this.param.value] == this.value)[this.param.key];
            //         }
            //     }
            // }
        }
    });
</script>
