<template>
  <Modal
    :isActive="isActive"
    title="参加確認"
    @close="close"
  >
    <p>
      参加するとすぐにコンテストが始まります。<br/>
      コンテスト時間は、参加から{{Math.floor(duration / 60)}}分です
    </p>
    <div slot="footer">
      <button class="button is-success" @click="myEnter">参加する</button>
      <button class="button" @click="close">キャンセル</button>
   </div>
   </Modal>
</template>

<script>
import { mapActions, mapState } from 'vuex';
import Modal from '@/components/common/Modal';

export default {
  props: [
    'isActive',
  ],
  computed: {
    ...mapState('koneko/contests', [
      'duration',
      'id',
    ]),
  },
  methods: {
    ...mapActions('koneko/contests', [
      'enter',
      'getContest',
    ]),
    async myEnter() {
      await this.enter();
      this.$emit('close');
    },
    close() {
      this.$emit('close');
    },
  },
  components: {
    Modal,
  },
};
</script>
