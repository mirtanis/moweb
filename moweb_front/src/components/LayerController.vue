<template>
  <div class="layer_container">
    <h2 v-if="page == 'waiting'" class="layer_title">참가자 목록</h2>
    <h2 v-else class="layer_title">순서 바꾸기</h2>
    <draggable
      class="layer_list"
      :list="dragLayerSequence"
      :move="checkMove"
      @end="onMove"
    >
      <transition-group>
        <div
          class="layer_item"
          v-for="(userName, index) in dragLayerSequence"
          :key="userName"
        >
          <div class="user_name">
            <div>
              <v-icon> mdi-menu-swap </v-icon>
              {{ userName }}
            </div>
            <div v-if="index === dragLayerSequence.length - 1">앞</div>
            <div v-else-if="index === 0">뒤</div>
          </div>
        </div>
      </transition-group>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  // 레이어 순서, 방장 여부, prop으로 받음
  props: ["layerSequence", "isAdmin", "roomNo", "page"],
  components: {
    draggable,
  },
  data: function () {
    return {
      dragLayerSequence: [],
    };
  },
  created() {
    this.dragLayerSequence = this.layerSequence.slice();
  },
  watch: {
    layerSequence() {
      this.dragLayerSequence = this.layerSequence.slice();
    },
  },
  methods: {
    checkMove() {
      // false 리턴하면 드래그 안됨
      return this.isAdmin;
    },
    onMove(event) {
      // 드래그 종료 이벤트
      // 이전 인덱스랑 새 인덱스가 다르면 layer 변경 websocket 호출
      if (event.newIndex !== event.oldIndex) {
        this.$emit("sendLayer", this.dragLayerSequence);
      }
    },
  },
};
</script>

<style>
.layer_container {
  padding: 8px 15px 0px 12px;
}
.layer_item {
  padding: 4px;
  vertical-align: middle;
  border-radius: 4px;
}
.layer_item:hover {
  background-color: #b3c9c5;
  cursor: pointer;
}
.layer_no {
  font-size: 1.2rem;
  box-sizing: border-box;
  display: inline-block;
  width: 24px;
  text-align: center;
}

.user_name {
  display: flex;
  justify-content: space-between;
  font-size: 1rem;
  font-weight: bold;
}
</style>
