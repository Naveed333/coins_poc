<template>
  <transition name="modal-fade">
    <div v-show="showModal" class="modal-mask" @click.self="$emit('close')">
      <div class="modal-wrapper">
        <div class="modal-container">
          <h3 class="modal-title">Select Columns</h3>
          <div v-for="option in options" :key="option.key" class="option">
            <input type="checkbox" v-model="selectedOptions" :value="option" class="option-checkbox">
            <label class="option-label">{{ option.label }}</label>
          </div>
          <div class="button-container">
            <button @click="save" class="save-button">Save</button>
            <button @click="$emit('close')" class="cancel-button">Cancel</button>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import { ref } from 'vue';

export default {
  props: ['options'],
  emits: ['save', 'close'],
  setup(props, { emit }) {
    const selectedOptions = ref([]);
    const showModal = ref(true);

    const save = () => {
      emit('save', selectedOptions.value);
      emit('close');
    };

    return { selectedOptions, showModal, save };
  },
};
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-wrapper {
  width: 100%;
  display: flex;
  justify-content: center;
}

.modal-container {
  width: 400px;
  max-height: 80vh;
  overflow-y: auto;
  background-color: #ffffff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease;
  color: #000000; 
}
.option-label{
  font-size: 12px;
  color: black;
  margin-bottom: 10px;
}

.modal-title {
  font-size: 20px;
  font-weight: bold;
  color: black;
  margin-bottom: 20px;
}

.option {
  margin-bottom: 10px;
}

.option-checkbox {
  margin-right: 10px;
}

.button-container {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.save-button,
.cancel-button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.save-button {
  background-color: #698d6b;
  color: #ffffff;
}

.save-button:hover {
  background-color: #388e3c;
}

.cancel-button {
  background-color: #de837d;
  color: #ffffff;
  margin-left: 10px;
}

.cancel-button:hover {
  background-color: #d32f2f;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.3s;
}

.modal-fade-enter, .modal-fade-leave-to {
  opacity: 0;
}
</style>
