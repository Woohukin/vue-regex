<template>
  <div class="binding">
    <div class="binding_btn">
      <input ref="edit" @input="_onInput($event)"  type="text" v-model="city"/>
    </div>
    <div style="position:absolute;width:100px;height:auto;
                left:10px;top:10px;background: #2c3e50" v-show="isShow">
      <p style="background-color: antiquewhite; margin: 4px" v-for="item in selectCitys" :key="item" @click="_onChoose(item)">{{ item }}</p>
    </div>
  </div>
</template>
<script>

export default {
  data() {
    return {
      // 解析后需要匹配的关键字
      search: "",
      // 绑定显示事件
      isShow: false,
      // 人为控制是否需要展示
      needShow:false,
      // 初始时，首次匹配%{标志位
      hasInit:false,
      // 输入框中实际内容
      city: "",
      // citys: ['北京', '北海', '东北', '上海', '武汉', '东京', '广州', '广元市', '上饶', '江岸区'],
      citys: ['bj', 'bh', 'db', 'sh', 'wh', 'dj', 'gz', 'gys', 'sr', 'jaq'],
      selectCitys: []
    }
  },

  mounted() {
    this.hasInit = true;
    // 首次可以全部展示
    this.selectCitys = this.citys;
  },

  methods: {
    _onChoose(item){
      this._hideCandidate()
      this.needShow = false;
      this.city += item;
    },

    _onCheckStr(content){
      const p = /%{([A-Za-z0-9_]*)$/g; // 匹配行尾为***%{格式
      return p.test(content);
    },

    _onInput() {
      // this.test();
      console.log(" 输入 ------ " + this.city);
      // 判断是否输入了 "xxx%{"
      if(this._onCheckStr(this.city)) {
        const reg = /%{([A-Za-z0-9_]+)$/g;
        // 再具体截取出 "%{**"相关值
        const result = this.city.match(reg);
        console.log("匹配成功,  解析-----> " + result);
        this._showCandidate(result);
      }else {
        this._hideCandidate();
      }
    },

    _showCandidate(result){
      this.isShow = true;
      if(null == result){
        this.search = "";
      } else if(result.length >= 0){
        var key = result[0].substring(2,result[0].length);
        console.log("搜索关键字-----> " + key);
        this.search = key;
      }
    },

    _hideCandidate(){
      this.isShow = false;
      this.search = "";
    },

    _test(){
      // sdgsdfg%{fvujg}fdsg%{gf}
      const reg  = /%{([\w]+)}$/g; // 提取行尾，包含%{***}的格式
      const reg2 = /%{(.*?)}/g; // 提取包含%{***}的格式
      const reg3 = /%{([A-Za-z0-9_]+)$/g; // 提取行尾，包含%{**的格式,且**为字母数字下划线
      // var result = reg.exec(this.city)
      console.log("----> " + this.city.match(reg));
      console.log("----> " + this.city.match(reg2));
      console.log("----> " + this.city.match(reg3));
    }
//
  },
  watch: {
    // eslint-disable-next-line no-unused-vars
    search: function (val, oldVal) {
      // 针对，点击候选列表情形
      console.log("----------- update " + val);
      if(!this.needShow){
        this.needShow = true;
        return ;
      }

      if (val.length === 0) {
        // 空内容，则全部显示
        this.selectCitys = this.citys;
      } else {
        const citys = [];
        // eslint-disable-next-line no-unused-vars
        this.citys.forEach((item, index) => {
          if (item.indexOf(val) >= 0) {
            citys.unshift(item)
          }
        })
        this.selectCitys = citys;
      }
    }
  }
}
</script>
<style scoped>
ul {
  border: 1px solid red;
}

li {
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid #ddd;
}

.binding_btn input {
  border: 1px solid #333;
  height: 44px;
  line-height: 44px;
}
</style>