<template>
  <div class="uiPerson" :class="{ active }">
    <div class="d-flex my-2">
      <div style="flex-grow: 1" class="mr-1">
        <input
          v-if="editing"
          ref="input"
          v-model="input"
          type="text"
          class="form-control"
          :placeholder="fallbackName"
          @change="done"
          @keydown.enter="done"
        >
        <button v-else class="uiPerson__button btn btn-default w-100 text-left" @click="click">
          <span class="uiPerson__name" :class="{ 'uiPerson__name--fallback': !personName }">{{ value || displayName }}</span>
          <span class="uiPerson__stats">{{ $tc('labels.numSheetsDay', round(total)) }}</span>
        </button>
      </div>
      <UiIconButton icon="pen" class="ml-1" :title="$t('actions.rename')" @click="edit" />
      <UiIconButton icon="times" class="ml-1" :title="$t('actions.remove')" :disabled="!removable" @click="remove" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: String,
    total: Number,
    active: Boolean,
    index: Number,
  },

  data () {
    return {
      editing: false
    }
  },

  computed: {
    input: {
      get () { return this.value },
      set (value) {
        this.$emit('input', value)
      }
    },

    removable () {
      return this.index > 0
    },

    personName () {
      return String(this.value || '').trim()
    },

    displayName () {
      return this.personName || this.fallbackName
    },

    fallbackName () {
      return this.$tc(this.index === 0 ? 'labels.personYou' : 'labels.personNum', this.index + 1)
    },
  },

  methods: {
    round (value = 0) {
      return Math.floor(value)
    },

    focus () {
      this.$el.querySelector('button').focus()
    },

    edit () {
      this.editing = true
      this.$nextTick(() => {
        const input = this.$refs.input
        input.addEventListener('blur', this.onBlur)
        input.focus()
        input.select()
        setTimeout(() => input.setSelectionRange(0, 9999)) // iOS
      })
    },

    done () {
      this.editing = false
      this.$emit('change')
      // this.$nextTick(() => this.focus())
    },

    click () {
      this.$emit('click')
    },

    remove () {
      this.$emit('remove')
    },

    onBlur () {
      if (this.$refs.input) {
        this.$refs.input.removeEventListener('blur', this.onBlur)
      }
      this.editing = false
    }
  }

}
</script>

<style lang="scss">
$light-blue: #007bff30;
$light-grey: #e2e6ea;

.uiPerson {
  transition: background-color;

  &__button {
    // background: $light-blue;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  &__name {
    max-width: calc(100vw - 270px);
    overflow-x: hidden;
    text-overflow: ellipsis;
  }

  &__stats {
    font-size: 70%;
  }

  & button {

    border-color: transparent;

    &[disabled] {
      opacity: 0.6;
      pointer-events: none;
    }

    &:hover {
      background-color: #f4f4f5;
    }

    &:hover {
      color: #212529;
      background-color: $light-blue;
      border-color: transparent;
    }
  }

  @media (hover: hover) {
    &:not(.active) button.uiPerson__button {
      background-color: transparent;
    }
  }

  button:hover {
    background-color: $light-blue !important;
  }

  &.active button {
    font-weight: bold;
    border-color: transparent !important;
    background-color: $light-blue !important;
  }
/*
  &.active {
    font-weight: bold;
    border-color: transparent;
    background-color: $light-blue !important;

    border-radius: 4px;
    box-shadow: 0 0 0 5px $light-blue;

    input:focus,
    button:focus {
      box-shadow: none;
    }
  }
*/

}
</style>
