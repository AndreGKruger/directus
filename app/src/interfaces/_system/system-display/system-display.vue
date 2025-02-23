<template>
	<v-notice v-if="selectedType === undefined">
		{{ t('select_field_type') }}
	</v-notice>
	<v-select
		v-else
		:items="items"
		:model-value="value"
		:placeholder="t('interfaces.system-display.placeholder')"
		show-deselect
		@update:model-value="$emit('input', $event)"
	/>
</template>

<script setup lang="ts">
import { useExtensions } from '@/extensions';
import { DisplayConfig } from '@directus/types';
import { computed, inject, ref, watch } from 'vue';
import { useI18n } from 'vue-i18n';

const props = defineProps<{
	value: string | null;
	typeField?: string;
}>();

const emit = defineEmits<{
	input: [value: string | null];
}>();

const { t } = useI18n();

const { displays } = useExtensions();

const values = inject('values', ref<Record<string, any>>({}));

const selectedType = computed(() => {
	if (!props.typeField || !values.value[props.typeField]) return;
	return values.value[props.typeField];
});

watch(
	() => values.value[props.typeField!],
	() => {
		emit('input', null);
	}
);

const items = computed(() => {
	return displays.value
		.filter((display: DisplayConfig) => (display.localTypes?.length ?? 0) === 0)
		.filter((display: DisplayConfig) => selectedType.value === undefined || display.types.includes(selectedType.value))
		.map((display: DisplayConfig) => {
			return {
				text: display.name,
				value: display.id,
			};
		});
});
</script>
