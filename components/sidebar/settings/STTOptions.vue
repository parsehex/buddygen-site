<script setup lang="ts">
import { computed, ref } from 'vue';
import { useAppStore } from '@/stores/main';
import {
	AccordionTrigger,
	AccordionItem,
	AccordionContent,
} from '@/components/ui/accordion';
import { Label } from '@/components/ui/label';
import { RadioGroup, RadioGroupItem } from '@/components/ui/radio-group';
import OptionSection from './OptionSection.vue';

const store = useAppStore();

const autoSendSTT = computed({
	get: () =>
		// @ts-ignore
		store.settings.auto_send_stt === 1 || store.settings.auto_send_stt === '1.0'
			? 'true'
			: 'false',
	set: (val: string) => {
		if (val === store.settings.auto_send_stt + '') return;
		const b = val === 'true';
		const num = b ? 1 : 0;
		if (num === store.settings.auto_send_stt) return;
		// @ts-ignore
		store.settings.auto_send_stt = num + '.0';
	},
});
</script>

<template>
	<AccordionItem value="stt-options">
		<AccordionTrigger>Speech-to-Text Options</AccordionTrigger>
		<AccordionContent>
			<OptionSection
				label="Auto Send STT"
				labelName="auto_send_stt"
				orientation="vertical"
			>
				<RadioGroup
					:default-value="autoSendSTT"
					v-model="autoSendSTT"
					id="auto_send_stt"
					class="flex flex-row"
				>
					<div class="flex items-center space-x-2">
						<RadioGroupItem id="auto-stt-yes" value="true">Yes</RadioGroupItem>
						<Label for="auto-stt-yes" class="block">Yes</Label>
					</div>
					<div class="flex items-center space-x-2">
						<RadioGroupItem id="auto-stt-no" value="false">No</RadioGroupItem>
						<Label for="auto-stt-no" class="block">No</Label>
					</div>
				</RadioGroup>
			</OptionSection>
		</AccordionContent>
	</AccordionItem>
</template>
