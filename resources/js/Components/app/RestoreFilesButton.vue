<template>
    <button
        @click="onClick"
        class="inline-flex items-center px-4 py-2 text-sm font-medium text-gray-900 bg-white border border-gray-200 rounded-lg hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700 dark:bg-gray-700 dark:border-gray-600 dark:text-white dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-blue-500 dark:focus:text-white"
    >
        <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="size-4 mr-2"
        >   
            <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M8.25 9V5.25A2.25 2.25 0 0 1 10.5 3h6a2.25 2.25 0 0 1 2.25 2.25v13.5A2.25 2.25 0 0 1 16.5 21h-6a2.25 2.25 0 0 1-2.25-2.25V15m-3 0-3-3m0 0 3-3m-3 3H15"
            />
        </svg>

        Восстановить
    </button>

    <ConfirmationDialog
        :show="showConfirmationDialog"
        message="Вы уверены, что хотите восстановить выбранные файлы?"
        @cancel="onCancel"
        @confirm="onConfirm"
    >
    </ConfirmationDialog>
</template>

<script setup>
import ConfirmationDialog from "@/Components/ConfirmationDialog.vue";
import { showErrorDialog, showSuccessNotification } from "@/event-bus";
import { useForm, usePage } from "@inertiajs/vue3";
import { ref } from "vue";

const page = usePage();
const form = useForm({
    all: null,
    ids: [],
    parent_id: null,
});

const showConfirmationDialog = ref(false);

const props = defineProps({
    allSelected: {
        type: Boolean,
        required: false,
        default: false,
    },
    selectedIds: {
        type: Array,
        required: false,
    },
});

const emit = defineEmits(["restore"]);

function onClick() {
    if (!props.allSelected && !props.selectedIds.length) {
        showErrorDialog("Пожалуйста, выберите хотя бы один файл для восстановления");
        return;
    }
    showConfirmationDialog.value = true;
}

function onCancel() {
    showConfirmationDialog.value = false;
}

function onConfirm() {
    if (props.allSelected) {
        form.all = true;
    } else {
        form.ids = props.selectedIds;
    }

    form.post(route("file.restore"), {
        onSuccess: () => {
            showConfirmationDialog.value = false;
            emit("restore");
            showSuccessNotification("Выбранный файл был восстановлен");
        },
    });
}
</script>

<style scoped></style>
