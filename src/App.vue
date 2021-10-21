<template>
  <div id="calcWrapper">
      <Display v-bind:displayValue=dispValue v-bind:subDisplayValue=subDispValue></Display>
      <Button   v-for="b in buttons" 
                v-bind:key="b.id" 
                v-bind:bValues="b"
                v-on:handle-press="handlePress" 
                v-bind:style="b.id === 1 ? bStyleObjectLarge:  bStyleObjectRegular">
      </Button>
  </div>
</template>

<script>
import Display from './components/Display.vue'
import Button from './components/Button.vue'

export default {
  data: function () {
      return {
          dispValue: "0",
          subDispValue: "",
          prevValue: "0",
          prevOp: "=",
          opInEffect: false,
          bStyleObjectRegular:{
              width: '25%'
          },
          bStyleObjectLarge:{
              width: '49.9%',
          },
          buttons:[
              {
                  id: 1,
                  name: "AC",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 2,
                  name: "C",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 3,
                  name: "/",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 4,
                  name: "7",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 5,
                  name: "8",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 6,
                  name: "9",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 7,
                  name: "x",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 8,
                  name: "4",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 9,
                  name: "5",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 10,
                  name: "6",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 11,
                  name: "+",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 12,
                  name: "1",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 13,
                  name: "2",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 14,
                  name: "3",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 15,
                  name: "-",
                  isRegister: false,
                  isOption: true
              },
              {
                  id: 16,
                  name: "0",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 17,
                  name: ".",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 18,
                  name: "\u00b1",
                  isRegister: true,
                  isOption: false
              },
              {
                  id: 19,
                  name: "=",
                  isRegister: false,
                  isOption: true
              }
          ]
      }
  },
  components:{
      Display,
      Button
  },
  methods: {
      handlePress: function (event){
          var number = event.textContent.trim();
          switch (number){
              case "AC": this.clearAll();
                break;
              case "C": this.clearDisplay();
                break;
              case "0":
              case "1":
              case "2":
              case "3":
              case "4":
              case "5":
              case "6":
              case "7":
              case "8":
              case "9": this.numberPressed(number);
              break; 
              case "+": this.computeOp("+");
                break;
              case "-": this.computeOp("-");
                break;
              case "/": this.computeOp("/");
                break;
              case "x": this.computeOp("x");
                break;
              case "=": this.equalPressed();
                break;
              case ".": this.addPoint();
                  break;
              case "\u00b1": this.negateValue();
                  break;
              default:
                  alert("KEY ERROR: in default");
          }
      },
      negateValue: function(){
          if (this.dispValue != "0"){
              if (this.dispValue.indexOf("-") < 0){
              this.dispValue = "-" + this.dispValue;
            }
            else{
                this.dispValue = this.dispValue.substring(1);
            }
          }
          
      },
      addPoint: function() {
          if (this.dispValue.indexOf(".") < 0){
              this.dispValue += "."
          }
      },
      equalPressed: function(){
          try{
              this.computeEqual(this.prevValue, this.dispValue, this.prevOp);
              this.subDispValue = "";
          }
          catch (e){
              alert(e);
          }
      },
      numberPressed: function(number){
          this.opInEffect = false;
          if (this.dispValue === "0"){
              this.dispValue = number;
          }
          else {
              if (this.dispValue.length >= 15){
                  alert("KEY ERROR: Display limit reached");
              }
              else{
                  this.dispValue += number;
              }
              
          }
          
      },
      computePlus: function (){
          this.dispValue = this.computeOps(this.prevValue, this.dispValue, "+")
      },
      computeSub: function (){
          this.dispValue = this.computeOps(this.prevValue, this.dispValue, "-")
      },
      computeDiv: function (){
          this.dispValue = this.computeOps(this.prevValue, this.dispValue, "/")
      },
      computeMult: function (){
          this.dispValue = this.computeOps(this.prevValue, this.dispValue, "x")
      },
      computeOp: function (op){
          if (!this.opInEffect){
            try{
                this.computeEqual(this.prevValue, this.dispValue, this.prevOp);
                this.prevValue = this.dispValue;
                this.dispValue = "0";
                this.prevOp = op;
                this.subDispValue = this.prevValue.toString() + " " + this.prevOp + "    ";
                this.opInEffect = true;
            }
            catch (e){
                alert(e);
            }
            
          }
          
      },
      computeEqual: function(op1, op2, op){
          let op1_num = parseFloat(op1);
          let op2_num = parseFloat(op2);
          let result = 0;
          if (op === "+"){
              result = op1_num + op2_num;
          }
          else if (op === "-"){
              result = op1_num - op2_num;
          }
          else if (op === "x"){
              result = op1_num * op2_num;
          }
          else if (op === "/"){
              if (op2_num == 0){
                  throw "MATH ERROR: Cannot divide by 0";
              }
              else{
                  result = op1_num / op2_num;
              }
              
          }
          else{
              result = op2_num;
          }
          let temp = result.toString();
          if (temp.length >= 15){
              throw "DISPLAY ERROR: Computation result will not fit on display. Use C or AC to perform a simpler computation.";
          }
          this.dispValue = temp;
          this.prevValue = "0";
          this.prevOp = "=";

      },
      clearDisplay: function (){
          this.dispValue = "0";
      },
      clearAll: function (){
          this.clearDisplay();
          this.prevValue = "0";
          this.prevOp = "=";
          this.subDispValue ="";
          this.opInEffect = false;
      }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
#calcWrapper {
  width: 100%;
  margin: auto;
  box-sizing: border-box;
  border: 5px solid black;
  border-radius: 5px;
  overflow: hidden;
}

@media screen and (min-width: 768px){
    #calcWrapper {
        width: 40%;
        margin: auto;
        border-color: 5px solid black;
    }
}
</style>