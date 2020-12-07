<template>
    <div class="input_address">
        <input type="text" id="inAddress" autocomplete="off" @click="ChangeFocus" :placeholder="brand == 'pldt_key' ? 'Input address here' : 'Enter City or Municipality' " :value="inputAddress">
        <div class="keyboard" v-if="keyboardToggle">
            <div :class="ToUpperCase ? 'keyrow tUppercase '+brand : 'keyrow '+brand">
            <button type="button" v-for="n in 10" @click="keyBoardFunc(n)" :key="n"><span v-if="n < 10">{{ n }}</span> <span v-else> 0</span></button>
            <button type="button" class="backspace" @click="keyBoardFunc('del')">del</button>
            </div>
            <div :class="ToUpperCase ? 'keyrow tUppercase '+brand : 'keyrow '+brand">
            <button type="button" v-for="value in QtoP" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
            <button type="button" class="backspace" @click="keyBoardFunc('caps')">Caps</button>
            </div>
            <div :class="ToUpperCase ? 'keyrow tUppercase '+brand : 'keyrow '+brand">
            <button type="button" v-for="value in AtoL" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
            <button type="button" class="enter" @click="keyBoardFunc('enter')">Enter</button>
            </div>
            <div :class="ToUpperCase ? 'keyrow tUppercase last_row '+brand : 'keyrow last_row '+brand">
            <button type="button" v-for="value in Ztoplus" @click="keyBoardFunc(value)" :key="value">{{ value }}</button>
            </div>
            <div :class="ToUpperCase ? 'keyrow tUppercase '+brand : 'keyrow '+brand">
            <button type="button" class="space" @click="keyBoardFunc('space')">space</button>
            </div>

        </div>
    </div>
</template>

<script>
export default {
    props: ["brand"],
    data(){
        return{
            QtoP: ['q','w','e','r','t','y','u','i','o','p'],
            AtoL: ['a','s','d','f','g','h','j','k','l'],
            Ztoplus: ['z','x','c','v','b','n','m',',','.','-','_','+'],
            keyboardToggle: false,
            inputAddress: '',
            Cache: true,
            CursorPosition: 0,
            ToUpperCase: false
        }
    },
    methods:{
        ChangeFocus: function(){
            const input = document.getElementById('inAddress');
            this.CursorPosition = this.inputAddress.slice(0, input.selectionStart).length - 1;
            this.Cache = false;
            this.keyboardToggle = true;
        },
        keyBoardFunc: function(val){
            const input = document.getElementById('inAddress');
            const inputval = this.inputAddress;
            const CursorStart = this.inputAddress.slice(0, input.selectionStart).length;
            const textLength = this.inputAddress.length;

            if(this.Cache){
                input.focus();
            }

            if(val == "del"){
                if(CursorStart == textLength){
                this.inputAddress = inputval.slice(0, -1);
                input.focus();
                // alert(1)
                }else{
                const textBefore = this.inputAddress.substring(0,  this.CursorPosition );
                const textAfter  = this.inputAddress.substring( this.CursorPosition + 1, textLength  );

                // Output
                this.inputAddress = textBefore+''+textAfter
                // alert(2)
                }
            }else if(val == "caps"){
                if(CursorStart == textLength){
                input.focus();
                }
                // Output
                this.ToUpperCase = !this.ToUpperCase;
            }else if(val == "space"){
                input.focus();
                // Output
                this.inputAddress += ' ';
            }else if(val == "enter"){
                this.keyboardToggle = false;
            }else{
                if(CursorStart == textLength){
                input.focus();
                }
                if(this.ToUpperCase){
                // Output
                this.inputAddress += val.toUpperCase();
                }else{
                // Only in first letter
                if(this.Cache){
                    this.CursorPosition = CursorStart;
                }else{
                    this.CursorPosition = this.inputAddress.slice(0, this.CursorPosition).length + 1;
                }

                const textBefore = this.inputAddress.substring(0,  this.CursorPosition );
                const textAfter  = this.inputAddress.substring( this.CursorPosition, textLength );
                // Output
                this.inputAddress = textBefore+''+val+''+textAfter;
                // alert(textBefore+''+val+''+textAfter);

                }
            }

        }
    }
}
</script>