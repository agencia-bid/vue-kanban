<template>
  <div class="drag-container">
    <ul class="drag-list">
      <li v-for="stage in stages" class="drag-column" :class="{['drag-column-' + stage.name]: true}" :key="stage.id">

        <!-- Stage Slot -->
        <span class="drag-column-header">
          <slot name="stage" :header="{ stage, blocks: getBlocks(stage.id) }">
            <h2>{{ stage.name }}</h2>
          </slot>
        </span>

        <div class="drag-options"></div>

        <ul class="drag-inner-list" ref="list" :data-stage-id="stage.id">
          <li class="drag-item" v-for="(block, index) in theBlocks = getBlocks(stage.id)" :data-block-id="block.id" :key="block.id">
            <slot
              name="block"
              :block="block"
              :index="index"
              :last-item="theBlocks.length -1 === index"
              :stage-id="stage.id"
            >
              <div>{{ block.id }}</div>
            </slot>
          </li>
        </ul>

        <div class="drag-column-footer">
            <slot :name="'footer-' + stage.id"></slot>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
  import dragula from 'dragula';

  export default {
    name: 'KanbanBoard',

    props: {
      stages: {},
      blocks: {},
    },
    data() {
      return {

      };
    },

    computed: {
      localBlocks() {
        return this.blocks;
      },
    },

    methods: {
      getBlocks(id) {
        return this.localBlocks.filter(block => block.stage_id === id);
      },
    },

    mounted() {
      dragula(this.$refs.list)
        .on('drag', (el) => {
          el.classList.add('is-moving');
        })
        .on('drop', (block, list) => {
          let index = 0;
          for (index = 0; index < list.children.length; index += 1) {
            if (list.children[index].classList.contains('is-moving')) break;
          }
          this.$emit('update-block', block.dataset.blockId, list.dataset.stageId);
        })
        .on('dragend', (el) => {
          el.classList.remove('is-moving');

          window.setTimeout(() => {
            el.classList.add('is-moved');
            window.setTimeout(() => {
              el.classList.remove('is-moved');
            }, 600);
          }, 100);
        });
    },
  };
</script>
