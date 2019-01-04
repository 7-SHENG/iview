<template>
    <div :class="wrapClasses">
        <template v-if="type !== 'textarea'">
            <div :class="[prefixCls + '-group-prepend']" v-if="prepend" v-show="slotReady"><slot name="prepend"></slot></div>
            <i class="ivu-icon" :class="['ivu-icon-ios-close', prefixCls + '-icon', prefixCls + '-icon-clear' , prefixCls + '-icon-normal']" v-if="clearable && currentValue" @click="handleClear"></i>
            <i class="ivu-icon" :class="['ivu-icon-' + icon, prefixCls + '-icon', prefixCls + '-icon-normal']" v-else-if="icon" @click="handleIconClick"></i>
            <transition name="fade">
                <i class="ivu-icon ivu-icon-load-c ivu-load-loop" :class="[prefixCls + '-icon', prefixCls + '-icon-validate']" v-if="!icon"></i>
            </transition>
            <input
                :id="elementId"
                :autocomplete="autocomplete"
                :spellcheck="spellcheck"
                ref="input"
                :type="type"
                :class="inputClasses"
                :placeholder="placeholder"
                :disabled="disabled"
                :maxlength="maxlength"
                :readonly="readonly"
                :name="name"
                :value="currentValue"
                :number="number"
                :autofocus="autofocus"
                @keyup.enter="handleEnter"
                @keyup="handleKeyup"
                @keypress="handleKeypress"
                @keydown="handleKeydown"
                @focus="handleFocus"
                @blur="handleBlur"
                @input="handleInput"
                @change="handleChange"
                @mouseup="handleMouseup"
                @mousedown="handleMousedown"
            >
            <div :class="[prefixCls + '-group-append']" v-if="append" v-show="slotReady"><slot name="append"></slot></div>
        </template>
        <textarea
            v-else
            :id="elementId"
            :wrap="wrap"
            :autocomplete="autocomplete"
            :spellcheck="spellcheck"
            ref="textarea"
            :class="textareaClasses"
            :style="textareaStyles"
            :placeholder="placeholder"
            :disabled="disabled"
            :rows="rows"
            :maxlength="maxlength"
            :readonly="readonly"
            :name="name"
            :value="currentValue"
            :autofocus="autofocus"
            @keyup.enter="handleEnter"
            @keyup="handleKeyup"
            @keypress="handleKeypress"
            @keydown="handleKeydown"
            @focus="handleFocus"
            @blur="handleBlur"
            @input="handleInput"
            @mouseup="handleMouseup"
            @mousedown="handleMousedown"
        >
        </textarea>
    </div>
</template>
<script>
    import { oneOf, findComponentUpward } from '../../utils/assist';
    import calcTextareaHeight from '../../utils/calcTextareaHeight';
    import Emitter from '../../mixins/emitter';

    const prefixCls = 'ivu-input';

    export default {
        name: 'Input',
        mixins: [ Emitter ],
        props: {
            type: {
                validator (value) {
                    return oneOf(value, ['text', 'textarea', 'password', 'url', 'email', 'date']);
                },
                default: 'text'
            },
            value: {
                type: [String, Number],
                default: ''
            },
            size: {
                validator (value) {
                    return oneOf(value, ['small', 'large', 'default']);
                }
            },
            placeholder: {
                type: String,
                default: ''
            },
            maxlength: {
                type: Number
            },
            disabled: {
                type: Boolean,
                default: false
            },
            icon: String,
            autosize: {
                type: [Boolean, Object],
                default: false
            },
            rows: {
                type: Number,
                default: 2
            },
            readonly: {
                type: Boolean,
                default: false
            },
            name: {
                type: String
            },
            number: {
                type: Boolean,
                default: false
            },
            autofocus: {
                type: Boolean,
                default: false
            },
            spellcheck: {
                type: Boolean,
                default: false
            },
            autocomplete: {
                validator (value) {
                    return oneOf(value, ['on', 'off']);
                },
                default: 'off'
            },
            clearable: {
                type: Boolean,
                default: false
            },
            elementId: {
                type: String
            },
            wrap: {
                validator (value) {
                    return oneOf(value, ['hard', 'soft']);
                },
                default: 'soft'
            },
            maxLengthOfRow: {
                type: Number,
                default: -1
            },
            maxLengthSpecialRow: {
                type: Object,
                default: () => {}
            },
            splitArr: {
                type: Array,
                default: null
            }
        },
        data () {
            return {
                currentValue: this.value,
                prefixCls: prefixCls,
                prepend: true,
                append: true,
                slotReady: false,
                textareaStyles: {},
                lineNum: 1
            };
        },
        computed: {
            wrapClasses () {
                return [
                    `${prefixCls}-wrapper`,
                    {
                        [`${prefixCls}-wrapper-${this.size}`]: !!this.size,
                        [`${prefixCls}-type`]: this.type,
                        [`${prefixCls}-group`]: this.prepend || this.append,
                        [`${prefixCls}-group-${this.size}`]: (this.prepend || this.append) && !!this.size,
                        [`${prefixCls}-group-with-prepend`]: this.prepend,
                        [`${prefixCls}-group-with-append`]: this.append,
                        [`${prefixCls}-hide-icon`]: this.append  // #554
                    }
                ];
            },
            inputClasses () {
                return [
                    `${prefixCls}`,
                    {
                        [`${prefixCls}-${this.size}`]: !!this.size,
                        [`${prefixCls}-disabled`]: this.disabled
                    }
                ];
            },
            textareaClasses () {
                return [
                    `${prefixCls}`,
                    {
                        [`${prefixCls}-disabled`]: this.disabled
                    }
                ];
            }
        },
        methods: {
            handleMouseup () {
                this.$emit('on-mouseup');
            },
            handleMousedown () {
                this.$emit('on-mousedown');
            },
            handleEnter (event) {
                this.$emit('on-enter', event);
            },
            handleKeydown (event) {
                this.$emit('on-keydown', event);
            },
            handleKeypress(event) {
                this.$emit('on-keypress', event);
            },
            handleKeyup (event) {
                this.$emit('on-keyup', event);
            },
            handleIconClick (event) {
                this.$emit('on-click', event);
            },
            handleFocus (event) {
                this.$emit('on-focus', event);
            },
            handleBlur (event) {
                this.$emit('on-blur', event);
                if (!findComponentUpward(this, ['DatePicker', 'TimePicker', 'Cascader', 'Search'])) {
                    this.dispatch('FormItem', 'on-form-blur', this.currentValue);
                }
            },
            handleInput (event) {
                let value = event.target.value;
                if (this.number) value = Number.isNaN(Number(value)) ? value : Number(value);
                this.$emit('input', value);
                this.setCurrentValue(value);
                this.$emit('on-change', event);
            },
            handleChange (event) {
                this.$emit('on-input-change', event);
            },
            setCurrentValue (value) {
                if (value === this.currentValue) return;
                this.$nextTick(() => {
                    this.resizeTextarea();
                });
                this.currentValue = value;
                if (!findComponentUpward(this, ['DatePicker', 'TimePicker', 'Cascader', 'Search'])) {
                    this.dispatch('FormItem', 'on-form-change', value);
                }
            },
            resizeTextarea () {
                const autosize = this.autosize;
                if (!autosize || this.type !== 'textarea') {
                    return false;
                }

                const minRows = autosize.minRows;
                const maxRows = autosize.maxRows;

                this.textareaStyles = calcTextareaHeight(this.$refs.textarea, minRows, maxRows);
            },
            focus () {
                if (this.type === 'textarea') {
                    this.$refs.textarea.focus();
                } else {
                    this.$refs.input.focus();
                }
            },
            blur () {
                if (this.type === 'textarea') {
                    this.$refs.textarea.blur();
                } else {
                    this.$refs.input.blur();
                }
            },
            handleClear () {
                const e = { target: { value: '' } };
                this.$emit('input', '');
                this.setCurrentValue('');
                this.$emit('on-change', e);
            },
            formatValue (value, sprlitArrs, maxLengthOfRow) {
                let newValue = '';
                let line_arr = value.split('\n');
                this.lineNum = 1;

                for(let i=0; i<line_arr.length; i++){
                    let tempLine = line_arr[i];

                    if(tempLine.length > this.getMaxLengthOfOneLine(this.lineNum)) {
                        debugger;
                        let tempSplitLineArr = this.splitLine(tempLine, sprlitArrs);

                        if(i !== (line_arr.length-1)) {
                            newValue = newValue + this.formatLine(tempSplitLineArr) + '\n';
                            this.lineNum = this.lineNum + 1;
                        }else {
                            newValue = newValue + this.formatLine(tempSplitLineArr);
                        }
                        // console.log(tempSplitLineArr);
                    }else {
                        if (i === (line_arr.length-1)) {
                            newValue = newValue + tempLine
                        }else {
                            newValue = newValue + tempLine + '\n';
                            this.lineNum = this.lineNum + 1;
                        }
                    }
                }

                return newValue;
            },
            getMaxLengthOfOneLine(){
                if (this.maxLengthSpecialRow === undefined || this.maxLengthSpecialRow === null){
                    return this.maxLengthOfRow;
                }

                let maxlengthOneRow = this.maxLengthSpecialRow[this.lineNum];
                if (maxlengthOneRow !== undefined && maxlengthOneRow !== null && !isNaN(maxlengthOneRow) && maxlengthOneRow > 0) {
                    return maxlengthOneRow;
                }else {
                    return this.maxLengthOfRow;
                }
            },
            formatLine (lineArr) {
                let targetStr = '';
                let oneLine = '';

                for(let i=0; i<lineArr.length; i++){
                    let tempStr = lineArr[i];
                    //单个单词长度大于maxLengthOfRow，将当个单词再次切分
                    let tempMaxLengthOfOneLine = this.getMaxLengthOfOneLine(this.lineNum);
                    if (tempStr.length > tempMaxLengthOfOneLine) {
                        oneLine === ''? targetStr = targetStr : targetStr = targetStr + oneLine + '\n';
                        let wordSplitNum = Math.ceil(tempStr.length/tempMaxLengthOfOneLine);
                        for (let j = 0; j < wordSplitNum; j++) {
                            if (j !== (wordSplitNum -1)) {
                                oneLine = tempStr.substring(j * tempMaxLengthOfOneLine, (j+1) * tempMaxLengthOfOneLine);
                                targetStr = targetStr + oneLine + '\n';
                                this.lineNum = this.lineNum + 1;
                                oneLine = '';
                            }else {
                                oneLine = tempStr.substring(j * tempMaxLengthOfOneLine, tempStr.length);
                            }
                        }
                    }else {
                        if((oneLine.length + lineArr[i].length) > tempMaxLengthOfOneLine) {
                            targetStr = targetStr + oneLine + '\n';
                            this.lineNum = this.lineNum + 1;
                            oneLine = lineArr[i].replace(/(^\s*)/g, '');
                        }else {
                            oneLine = oneLine + lineArr[i];
                        }
                    }
                }

                if(oneLine !== '') {
                    if(oneLine.charAt(0) === ' '){
                        oneLine = oneLine.replace(/(^\s*)/g, '');
                    }
                    targetStr = targetStr + oneLine;
                }

                return targetStr;
            },
            splitLine (line, sprlitArrs) {
                let parts = new Array();
                let startIndex = 0;

                while (true)
                {
                    let index = this.indexOfAny(line, sprlitArrs, startIndex, line.length);

                    if (index === -1) {
                        parts.push(line.substring(startIndex));
                        return parts;
                    }

                    let word = line.substring(startIndex, index);
                    let nextChar = line.charAt(index);
                    // Dashes and the likes should stick to the word occuring before it. Whitespace doesn't have to.
                    if (nextChar === ' ')
                    {
                        parts.push(word);
                        parts.push(nextChar);
                    }else {
                        parts.push(word + nextChar);
                    }

                    startIndex = index + 1;
                }

            },
            indexOfAny (str, anyOf /*Array*/, startIndex /*uint*/, count /*int*/) /*int*/ {

                startIndex = isNaN(startIndex) ? 0 : startIndex;
                if (startIndex < 0) {
                    startIndex = 0;
                }

                if (this == null || this == "") return -1;
                count = isNaN(count) ? -1 : ((count >= 0) ? count : -1);
                var l = str.length;
                var endIndex /*int*/;
                if ((count < 0) || (count > l - startIndex)) {
                    endIndex = l;
                } else {
                    endIndex = startIndex + count;
                }

                if (anyOf == null || anyOf == undefined) return -1;

                for (var i = startIndex; i <= endIndex ; i++) {
                    var curtxt = str.substring(startIndex, i);

                    var allindex = [];
                    for (var j = 0; j < anyOf.length; j++) {
                        var any = anyOf[j];
                        var index = curtxt.indexOf('' + any);
                        if (index > -1) {
                            return index + startIndex;
                        }
                    }
                }

                return -1;

            },
        },
        watch: {
            value (val) {
                this.setCurrentValue(val);
            },
            currentValue (newVal, oldVal) {
                if (newVal !== oldVal && newVal !== '' && this.type === 'textarea') {
                    if(this.maxLengthOfRow !== undefined && this.maxLengthOfRow !== null && this.maxLengthOfRow > 0 &&
                        this.splitArr !== undefined && this.splitArr !== null && this.splitArr.length > 0) {
                        let formatedVal = this.formatValue(newVal, this.splitArr, this.maxLengthOfRow);
                        this.setCurrentValue(formatedVal);
                    }else {
                        this.setCurrentValue(newVal);
                    }
                }else {
                }
            }
        },
        mounted () {
            if (this.type !== 'textarea') {
                this.prepend = this.$slots.prepend !== undefined;
                this.append = this.$slots.append !== undefined;
            } else {
                this.prepend = false;
                this.append = false;
            }
            this.slotReady = true;
            this.resizeTextarea();
        }
    };
</script>
