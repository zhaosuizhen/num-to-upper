<template>
  <div class="hello">
    <input type="text" v-model.number.trim="num" v-focus>
    <p>{{money}}</p>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      money:'',
      num:null
    }
  },
  methods:{
    moneyToCN(){
        const money = this.num;
        
        // 先判断是不是number类型，不是的话给出提示，结束函数。
        if(!(typeof money === 'number' || money === "" || money === null || money === undefined)){
          alert('你得输个数字')
          this.num = ""
          return
        }
        
        // 大数级位，从个位起每四个数位都是一级
        const bigDigit = ['', '万', '亿', '万亿']
        // 数字汉字
        const numCN = ['零', '一', '二', '三', '四', '五', '六', '七', '八', '九']
        // 小数级位
        const smallDigit = ['', '十', '百', '千']
        // 转成字符串
        let strMoney = money + ''
        // 通过小数点分成整数部分和小数部分
        let arrMoney = strMoney.split('.')

        // 现在是字符串类型！！
        let integerMoney = arrMoney[0]
        let decimalMoney = arrMoney[1]
        
        // 整数部分的长度
        let integerMoneyLen = integerMoney.length

        // 大数位的个数
        let bigDigitLen = Math.ceil(integerMoneyLen / 4)
        
        // 定义空数组用来存放每一个大数位
        let arrIntBigDigit = []

        // 将整数部分从各位开始，每四个分为一个数位，存到数组中
        for (let i = 0; i < bigDigitLen; i++) {// 有几个数位就循环几次
          arrIntBigDigit.push(
                      !i?// 判断是否为第一次循环，
                      integerMoney.slice(0 , integerMoneyLen % 4 == 0? 4 : integerMoneyLen % 4)://如果是第一次循环，就截取最大的数位
                      integerMoney.slice( 
                                     ( (integerMoneyLen % 4 == 0? 4 : integerMoneyLen % 4) + (i-1) * 4) ,
                                     ( (integerMoneyLen % 4 == 0? 4 : integerMoneyLen % 4) + (i) * 4) 
                                   ) //不是第一次循环，就从第二大数位开始，每四个截一次
                    )

          // 现在数组中的成员是字符串类型！！
          // console.log(arrIntBigDigit);
          
        }


        let arrBigDigit = arrIntBigDigit.map((item, index) => {
          
          // 将arrIntBigDigit数组中每一个成员转成汉字格式
          let arrSmallDigit = item.split('').map((val, key) => {// 先将字符串传为数组，遍历转换
          // 如果当前成员是字符串“0”，只把他换成汉字“零”。
          //如果不是“0”，把他转成对应的汉字后再加上小数位的单位
          // 例如['一千','两百','三十','四']
            return val === '0' ? numCN[Number(val)] : (numCN[Number(val)] + smallDigit[item.length - key - 1])
            
          }).join('').replace(/\u96f6+$/, '')//拼接起来，并将末尾的“零”删掉

          // console.log(arrSmallDigit);
  

          // 因为上面把末尾的零删了，如果出现四个都是零的情况，当前项就为空了
          return arrSmallDigit.length ?  //判断当前项是否为空
                 (arrSmallDigit + bigDigit[bigDigitLen - 1 - index]) : //不为空的话，给当前项加上对应的大数位
                 numCN[0]//如果为空，就让他变成汉字“零”，如果改的是是最末尾的数，会在最后把多余的“零”删掉
        })

        // console.log(arrBigDigit);
        
        // 最终结果：把上面处理好的每一个数级拼接起来，并处理“零”的格式：
        // 1.删除所有开头的“零”
        // 2.删除所有末尾的“零”
        // 3.将所有连着的“零”，换成一个“零”
        // 4.将“一十”改为“十”
        let integerRes = arrBigDigit.join('').replace(/^\u96f6+/, '')
                               .replace(/\u96f6+$/, '')
                               .replace(/\u96f6+/g, '\u96f6')
                               .replace(/^\u4e00\u5341/, '\u5341')

/***************************************整数部分处理完成***************************************/


        let decimalRes = ''
        // 判断是否有小数部分，或者小数部分为0/00，是的话把小数部分设置为“整”字
        if (!decimalMoney || decimalMoney === '0' || decimalMoney === '00') {
          decimalRes = '整'
        } else {
          let [p0, p1] = decimalMoney.split('')
          decimalRes = (p0 === '0' ? '零' : (numCN[Number(p0)] + '角')) + 
                      (!p1 || p1 === '0' ? '' : (numCN[Number(p1)] + '分'))//如果没有分，或者分为“0”的时候为空，
        }
/***************************************小数部分处理完成***************************************/

        // 如果整数部分有值的话就用整数部分,没有整数部分用“零”
        return `${integerRes || numCN[0]}元${decimalRes}`
    }




  },
  // 监测数据改变
  watch:{
    num(newVal){
      this.num = newVal
      this.money = this.moneyToCN()
    }
  },
  // 自动获取焦点
  directives:{
    focus:{
      inserted: function (dom) {
        dom.focus()
      }
    }
  },
  // 加载完成将数据初始化为数字0
  mounted(){
    this.num = 0*1
  }
    
}
</script>


<style scoped>

</style>
