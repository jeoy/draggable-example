<template>
  <div class="fluid container">
      {{ test }}
    <div class="form-group form-group-lg panel panel-default">
      <div class="panel-heading">
        <h3 class="panel-title">Sortbale control</h3>
      </div>
      <div class="panel-body">
        <div class = "checkbox">
          <!-- <label><input type = "checkbox" v-model="editable">Enable drag and drop</label> -->
        </div>
        <button type="button" class="btn btn-default" @click="orderList">Sort by original order</button>
      </div>
    </div>
    <div  v-for="oneItem in itemListAll" class="m-box m-column-config">
        <div class="column-config-name">{{oneItem.config.name}}</div>
        <div v-bind:class="'selected-' + oneItem.config.key">
            <div v-show="!oneItem.data.length" class="placeholder-item">请将需要展示的维度或指标拖拽到此处</div>

            <draggable v-model="oneItem.data" :options="dragOptions" :move="onMove"   @start="onStart" @end="onEnd">
                 <transition-group  class="list-group" type="transition" tag="ul"  :id="oneItem.id">
                    <column-config v-for="(Item, i) in oneItem.data"
                                    :config="Item.name"
                                    v-bind:key="i"
                                    >
                     </column-config>
                 </transition-group>
            </draggable>
        </div>
    </div>
    <div  class=" col-md-3">
      <pre>{{listRowString}}</pre>
    </div>

    <div  class=" col-md-3">
     <pre>{{listCategoryString}}</pre>
   </div>
   <div  class=" col-md-3">
    <pre>{{listColumnString}}</pre>
  </div>
    <div  class=" col-md-3">
     <pre>{{itemListAllString}}</pre>
   </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable'
import columnConfig from './columnConfig.vue';
import _ from 'lodash';

const listRow = [ {
        name: 'vue.draggable',
        key: '[vue.draggable]'
    }, {
        name: 'draggable',
        key: '[draggable]'
    }];
const listColumn = [ {
        name: '妙蛙种子',
        key: '[Bulbasaur]'
    }, {
        name: '皮卡丘',
        key: '[Pikachu]'
    }, {
        name: '小火龙',
        key: '[Charmander]'
    }];

const listCategory = [ {
        name: '兰浩青',
        key: '[lanhaoqing]'
    }, {
        name: '罗尖',
        key: '[luojian]'
    }, {
        name: '方堃',
        key: '[fangkun]'
    }];

const listData = [ {
        name: 'vue.draggable',
        key: '[vue.draggable]',
        attr: 'other info43'
    }, {
        name: 'draggable',
        key: '[draggable]',
        attr: 'other info'
    }, {
        name: '兰浩青',
        key: '[lanhaoqing]'
    }, {
        name: '罗尖',
        key: '[luojian]'
    }, {
        name: '方堃',
        key: '[fangkun]'
    }, {
        name: '妙蛙种子',
        key: '[Bulbasaur]',
        attr: 'other info Bulbasaur'
    }, {
        name: '皮卡丘',
        key: '[Pikachu]',
        attr: 'other info Pikachu'
    }, {
        name: '小火龙',
        key: '[Charmander]',
        attr: 'other info Charmander'
    }];


export default {
  name: 'hello',
  components: {
    draggable,
    columnConfig
  },
  data () {
    return {
        test: 'test tttt',
        config: [{
            name: '维度',
            key: 'row'
        }, {
            name: '分类',
            key: 'category'
        },{
            name: '数值',
            key: 'column'
        }],
        itemListAll: null,
        listData: listData,
        listRecall: {
            row: [],
            category: [],
            column: []
        },
        editable:true,
        isCollide: false,
        isInto: false,
        dragOptions: {
            group: {
                name: "columns",
                put: "columns",
                revertClone: true,
            },
            delay: 0,
            forceFallback: true,  // 调用mousemove，而不是直接用HTML5 DnD 方式，否则不能触发底层的mouseenter
            ghostClass: 'ghost',
            // delay: 500,
            chosenClass: 'chosen',
            animation: 100
        }
      }
  },
  created() {
      this.updateItemList();
  },
  mounted() {

      this.test = '3243423';
      var users = [
        { 'user': 'barney',  'age': 36, 'active': true },
        { 'user': 'fred',    'age': 40, 'active': false },
        { 'user': 'pebbles', 'age': 1,  'active': true }
      ];
       
     users =  _.sortBy(users, [ 'age' ]);
      console.log(users);
    // this.$nextTick(function(){
    //     this.changeText() ;
    // })
  },
  methods:{
      changeText() {
          this.test = '3243423';
      },
    orderList () {
      this.list = this.list.sort((one,two) =>{return one.order-two.order; })
    },
    onMove (evt, originalEvent) {
        this.isCollide = true;
        return true;
    },
    onStart(e) {
        ({
            row: {
                data: this.listRecall.row
            },
            category: {
                data: this.listRecall.category
            },
            column: {
                data: this.listRecall.column
            }
        } = this.itemListAll);
        this.bindEvents(e.from.id);
    },
    onEnd(e) {
        this.detachEvents();
        if (!this.isCollide) {
            if (!this.isInto) {
                // 未进入可放置区域，进行撤回最初的位置
                ['row', 'category', 'column'].forEach(key => {
                    this.itemListAll[key].data = this.listRecall[key];
                })
            }
        }
        // let columnConfig = this.widgetSetting.option.columnConfig;
        // columnConfig.row = this.itemListAll.row.data;
        // columnConfig.column = this.itemListAll.column.data;
        this.isInto = false;
        let listDataTemp = _.concat([],this.itemListAll.row.data, this.itemListAll.category.data, this.itemListAll.column.data);
        let index = 0;
        _.forEach(listDataTemp, o => {
            _.find(this.listData, val => {
                return val.key === o.key
            }).index = index
            index++;
        })

        this.listData = _.sortBy(this.listData, 'index' );
        // console.log(arrAll);
    },
    draggerIn() {
        // this.dragOptions.ghostClass = 'ghost';
        console.log('in');
        this.isInto = true;
    },
    draggerOut() {
        // this.dragOptions.ghostClass = 'ghost-transparent';
        console.log('out');
        this.isInto = false;
        this.isCollide = false;
    },
    bindEvents(id) {
        ['row', 'category', 'column'].forEach(key => {
            document.body.querySelector('.selected-' + key).addEventListener('mouseenter', this.draggerIn);
            document.body.querySelector('.selected-' + key).addEventListener('mouseleave', this.draggerOut);
        })

    },
    detachEvents() {
        ['row', 'category', 'column'].forEach(key => {
            document.body.querySelector('.selected-' + key).removeEventListener('mouseenter', this.draggerIn);
            document.body.querySelector('.selected-' + key).removeEventListener('mouseleave', this.draggerOut);
        });
    },
    updateItemList() {
        this.itemListAll = {
            'row': {
                'data': listRow,
                'config': this.config[0]
            },
            'category': {
                'data': listCategory,
                'config': this.config[1]
            },
            'column': {
                'data': listColumn,
                'config': this.config[2]
            }
        };
    }
  },
  computed: {
    listRowString(){
        if (this.itemListAll) {
            return JSON.stringify(this.itemListAll.row, null, 2);
        }
    },
    listCategoryString(){
        if (this.itemListAll) {
            return JSON.stringify(this.itemListAll.category, null, 2);
        }
    },
    listColumnString(){
        if (this.itemListAll) {
            return JSON.stringify(this.itemListAll.column, null, 2);
        }
    },
    itemListAllString(){
        return JSON.stringify(this.listData, null, 2);
    }
  },
  watch: {
  }
}
</script>

<style>
.flip-list-move {
  transition: transform 0.5s;
}
.col-md-3 {
    /*min-height: 800px !important;*/
    /*pointer-events: none;*/
}

.dragClass {
    /*background-color: red !important;*/
    color: red;
    pointer-events: none;
}
.no-move {
  transition: transform 0s;
}


.ghost {
  opacity: 0.5;

  /*background: ;*/
}
.ghost-transparent {
    opacity: 0.1;
}
.chosen {
  color: #000;
  /*background-color: #c00;*/
}

.list-group {
    margin-bottom: 5px !important;
  min-height: 40px;
  color: white;
}

.list-group-item {
  cursor: move;
  /*display: inline-block !important;*/
  background-color: #5ec264 !important;
}
.aaa{
    /*width: 100% !important;*/
    float: left;
}
.placeholder-item {
    position: absolute;
}
.list-group-item i{
  cursor: pointer;
}
</style>
