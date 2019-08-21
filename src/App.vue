<template>
  <div id="app">
    <section class="section">
      <h4>
        Vue adoptation of Ettric's
        <a href="//codepen.io/ettrics/pen/QbPEeg">Codepen</a>
      </h4>
    </section>
    <Kanban :stages="statuses" :blocks="blocks" @update-block="updateBlock">
      <template v-slot:stage="{ stage }">
          <h2>
            {{ stage.name }}
            <a>+</a>
          </h2>
      </template>

      <template v-slot:block="{ block }">
        <div>
          <strong>id:</strong> {{ block.id }}
        </div>
        <div>
          {{ block.title }}
        </div>
      </template>

      <div v-for="stage in statuses" :key="stage.id" :slot="`footer-${stage.name}`">
          <a href="" @click.prevent="() => addBlock(stage.id)">+ Add new block</a>
      </div>
    </Kanban>
  </div>
</template>

<script>
import faker from 'faker';
import { debounce } from 'lodash';
import Kanban from './components/Kanban';

export default {
  name: 'app',
  components: {
    Kanban,
  },
  data() {
    return {
      statuses: [
        {
          id: 1,
          name: 'on-hold',
        },
        {
          id: 2,
          name: 'in-progress',
        },
        {
          id: 3,
          name: 'needs-review',
        },
        {
          id: 4,
          name: 'approved',
        },
      ],
      blocks: [],
    };
  },
  mounted() {
    for (let i = 0; i <= 10; i += 1) {
      this.blocks.push({
        id: i,
        stage_id: this.statuses[Math.floor(Math.random() * 4)].id,
        title: faker.company.bs(),
      });
    }
  },

  methods: {
    updateBlock: debounce(function (id, stageId) {
      this.blocks.find(b => b.id === Number(id)).stage_id = parseInt(stageId, 10);
    }, 500),
    addBlock: debounce(function (stageId) {
      this.blocks.push({
        id: this.blocks.length,
        stage_id: stageId,
        title: faker.company.bs(),
      });
    }, 500),
  },
};
</script>

<style lang="scss">
  @import './assets/kanban.scss';

  $on-hold: #FB7D44;
  $in-progress: #2A92BF;
  $needs-review: #F4CE46;
  $approved: #00B961;

  * {
    box-sizing: border-box;
  }

  body {
    background: #33363D;
    color: white;
    font-family: 'Lato';
    font-weight: 300;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  .drag-column {
    .drag-column-header > div {
      width: 100%;
      h2 > a {
        float: right;
      }
    }

    .drag-column-footer > div {
        margin-left: 10px;
        a {
            text-decoration: none;
            color: white;
            &:hover {
                text-decoration: underline;
            }
        }
    }

    &-on-hold {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $on-hold;
      }
    }

    &-in-progress {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $in-progress;
      }
    }

    &-needs-review {
      .drag-column-header,
      .is-moved,
      .drag-options{
        background: $needs-review;
      }
    }

    &-approved {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $approved;
      }
    }
  }

  .section {
    padding: 20px;
    text-align: center;

    a {
      color: white;
      text-decoration: none;
      font-weight: 300;
    }

    h4 {
      font-weight: 400;
      a {
        font-weight: 600;
      }
    }
  }
</style>
