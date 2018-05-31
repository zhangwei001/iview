<template>
    <div>
        <div :class="classes" :style="styles">
            <slot></slot>
        </div>
    </div>
</template>
<script>
/*
  scrollHandle(event){
        var timer ,that = this,pageNum=0;
        let cls="";
          if(this.$route.name=="newDetail"){
                cls=".d-panel";
          }else{
                cls=".slide-content";
          }

        //设置分页 初期显示3条 后面每次20条数据
        timer && clearTimeout(timer);
        timer =setTimeout(function(){
              let scrollTop = event.target.scrollTop || 0; //滚动了的距离
              let scrollHeight= document.querySelector(cls).scrollHeight || 0; //滚动条全长
              let docHeight = document.documentElement.clientHeight || 0;//可视区高度
              //下滚
              if(that.tempDistance<scrollTop){

                that.$store.commit(types.PROGRESSPAGE,{
                    startId: that.progressDataList[that.progressDataList.length-1] ? 
                                  that.progressDataList[that.progressDataList.length-1].id :0,
                    limit: 20,
                    order:1 
                  });

                //可用分页次数
                pageNum = that.progressTotalNum>3 ? 
                            Math.ceil( (that.progressTotalNum-3)/that.progressPage.limit ):1;
                 
                if(scrollHeight == (scrollTop+ docHeight ) && that.pageNum > that.currentPageNum){
                    that.currentPageNum ++;
                    that.$store.dispatch("getProgressListData",{ 
                            id:        that.detailData.id,
                            order:     1,
                            start_id:  that.progressPage.startId,
                            limit:     that.progressPage.limit
                    }) 
                }
              }
              that.tempDistance = scrollTop;
            },800)
        },


*/

    import { on, off } from '../../utils/dom';
    const prefixCls = 'ivu-affix';

    function getScroll(target, top) {
        const prop = top ? 'pageYOffset' : 'pageXOffset';
        const method = top ? 'scrollTop' : 'scrollLeft';

        let ret = target[prop];

        if (typeof ret !== 'number') {
            ret = window.document.documentElement[method];
        }

        return ret;
    }

    function getOffset(element) {
        const rect = element.getBoundingClientRect();

        const scrollTop = getScroll(window, true);
        const scrollLeft = getScroll(window);

        const docEl = window.document.body;
        const clientTop = docEl.clientTop || 0;
        const clientLeft = docEl.clientLeft || 0;

        return {
            top: rect.top + scrollTop - clientTop,
            left: rect.left + scrollLeft - clientLeft
        };
    }

    export default {
        name: 'Affix',
        props: {
            offsetTop: {
                type: Number,
                default: 0
            },
            offsetBottom: {
                type: Number
            }
        },
        data () {
            return {
                affix: false,
                styles: {}
            };
        },
        computed: {
            offsetType () {
                let type = 'top';
                if (this.offsetBottom >= 0) {
                    type = 'bottom';
                }

                return type;
            },
            classes () {
                return [
                    {
                        [`${prefixCls}`]: this.affix
                    }
                ];
            }
        },
        mounted () {
//            window.addEventListener('scroll', this.handleScroll, false);
//            window.addEventListener('resize', this.handleScroll, false);
            on(window, 'scroll', this.handleScroll);
            on(window, 'resize', this.handleScroll);
        },
        beforeDestroy () {
//            window.removeEventListener('scroll', this.handleScroll, false);
//            window.removeEventListener('resize', this.handleScroll, false);
            off(window, 'scroll', this.handleScroll);
            off(window, 'resize', this.handleScroll);
        },
        methods: {
            handleScroll () {
                const affix = this.affix;
                const scrollTop = getScroll(window, true);
                const elOffset = getOffset(this.$el);
                const windowHeight = window.innerHeight;
                const elHeight = this.$el.getElementsByTagName('div')[0].offsetHeight;

                // Fixed Top
                if ((elOffset.top - this.offsetTop) < scrollTop && this.offsetType == 'top' && !affix) {
                    this.affix = true;
                    this.styles = {
                        top: `${this.offsetTop}px`,
                        left: `${elOffset.left}px`,
                        width: `${this.$el.offsetWidth}px`
                    };

                    this.$emit('on-change', true);
                } else if ((elOffset.top - this.offsetTop) > scrollTop && this.offsetType == 'top' && affix) {
                    this.affix = false;
                    this.styles = null;

                    this.$emit('on-change', false);
                }

                // Fixed Bottom
                if ((elOffset.top + this.offsetBottom + elHeight) > (scrollTop + windowHeight) && this.offsetType == 'bottom' && !affix) {
                    this.affix = true;
                    this.styles = {
                        bottom: `${this.offsetBottom}px`,
                        left: `${elOffset.left}px`,
                        width: `${this.$el.offsetWidth}px`
                    };

                    this.$emit('on-change', true);
                } else if ((elOffset.top + this.offsetBottom + elHeight) < (scrollTop + windowHeight) && this.offsetType == 'bottom' && affix) {
                    this.affix = false;
                    this.styles = null;

                    this.$emit('on-change', false);
                }
            }
        }
    };
</script>
