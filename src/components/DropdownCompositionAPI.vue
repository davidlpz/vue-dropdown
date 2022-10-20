<template>
    <div class="dropdown">
        <button class="dropdown-button" @click="toggle">
            <slot name="dropdown-button">
                {{ selectedOption }}
            </slot>
            <svg class="dropdown-button-caret" viewBox="0 0 30 30" xmlns="http://www.w3.org/2000/svg">
                <path d="M6.5 9.1l8.3 8.3L23.2 9l1.6 1.6-9.9 9.9L5 10.6l1.5-1.5z" fill="#1E1E1E"/>
            </svg>
        </button>
        <transition name="fade" appear>
            <div class="dropdown-menu" v-show="isOpen">
                <slot name="dropdown-options">
                    <a
                        v-for="option, index in options"
                        :key="index"
                        class="dropdown-option"
                        :class="{ selected: isSelected(option) }"
                        @click="setOption(option)"
                    >
                        {{ typeof option === 'string' ? option : option.name }}
                    </a>
                </slot>
                <slot name="dropdown-content"></slot>
            </div>
        </transition>
    </div>    
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, getCurrentInstance } from 'vue';

const props = defineProps({
    disabled: {
        type: Boolean,
        default: false
    },
    selected: {
        type: [Object, String]
    },
    options: {
        type: Array,
        default: function () {
            return [];
        }
    }
});

const emit = defineEmits({
    setOption(option) {
        return typeof option === 'string' || typeof option.name === 'string';
    }
});

const vm = getCurrentInstance();
const isOpen = ref(false);

/**
 * Get the name of the selected option
 *
 * @return {String}
 */
const selectedOption = computed(() => {
    const selected = !props.selected && props.options.length ? 'Select an option' : props.selected;

    return (typeof selected === 'object') ? selected.name : selected;
});

/**
 * Open/close the dropdown
 */
const toggle = () => {
    isOpen.value = props.disabled ? false : !isOpen.value;
}

/**
 * Close the dropdown when clicking outside the element
 *
 * @param {Object} e
 */
const close = (e) => {
    const { target } = e;
    const isClickInside = vm.subTree.children.some(element => {
        const { el } = element;
        return target === el || el.contains(target);
    });

    if (!isClickInside) {
        isOpen.value = false;
    }
}

/**
 * Emit the event to select the given option
 *
 * @param {String}
 */
const setOption = (option) => {
    emit('setOption', option);
    isOpen.value = false;
}

/**
 * Check if the given option is selected
 *
 * @param {Object|String}
 *
 * @return {Boolean}
 */
const isSelected = (option) => {
    if (props.options.length === 0 || props.selected === null) {
        return false;
    }

    if (typeof option === 'string') {
        return props.selected === option;
    } else if (typeof option === 'object' && option !== null) {
        return props.selected.id === option.id;
    }

    return false;
}

onMounted(() => {
    document.addEventListener('click', close);
});

onBeforeUnmount(() => {
    document.removeEventListener('click', close);
});

</script>

<style>
:root {
    --main-bg-color: #fff;
    --selected-bg-color: #d0d7de;
    --hover-bg-color: #e8eef5;
    --main-border-color: #e0e3e6;
}
</style>

<style lang="scss">
.dropdown {
    position: relative;
    min-width: 125px;
    width: 235px;
    font-size: .875rem;
    font-weight: 500;
    text-align: left;
    background-color: var(--main-bg-color);
    user-select: none;
    & + & {
        margin-left: 1rem;
    }
}
.dropdown-button {
    position: relative;
    width: 100%;
    padding: .625rem 2rem .625rem .875rem;
    font: inherit;
    color: inherit;
    text-align: left;
    text-overflow: ellipsis;
    white-space: nowrap;
    background: transparent;
    overflow: hidden;
    border: 1px solid transparent;
    border-radius: 2px;
    outline: none; 
    cursor: pointer;
}
.dropdown-button-caret {
    position: absolute;
    top: 50%;
    right: .4rem;
    transform: translate(0, -50%) scale(1);
    width: 1.25rem;
    height: 1.25rem;
    transition: .25s;
}
.dropdown-menu {
    position: absolute;
    top: 100%;
    left: 0;
    width: 100%;
    padding: .3125rem 0;
    max-height: 270px;
    background: inherit;
    border-radius: 2px;
    box-shadow: 1px 2px 4px 0 var(--selected-bg-color);
    transition: opacity .25s ease-in, visibility .25s ease-in;
    overflow-y: auto;
    z-index: 9999;
}
.dropdown-option {
    display: block;
    padding: .5rem .75rem;
    color: inherit;
    text-decoration: none;
    text-transform: capitalize;
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
    cursor: pointer;
    transition: background-color .25s;
    &.selected {
        background-color: var(--selected-bg-color);
    }
    &:hover {
        background-color: var(--hover-bg-color);
    }
}

.dropdown.bordered .dropdown-button {
    border: 1px solid #e0e3e6;
}

.fade-enter-active,
.fade-leave-active {
    transition: all .25s;
}
.fade-enter,
.fade-leave-to {
    opacity: 0;
}
</style>