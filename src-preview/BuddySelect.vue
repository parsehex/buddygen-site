<script setup lang="ts">
import { ref } from 'vue';
import { Button } from '@/components/ui/button';
import {
	Select,
	SelectContent,
	SelectGroup,
	SelectItem,
	SelectLabel,
	SelectTrigger,
	SelectValue,
} from '@/components/ui/select';
import { useAppStore } from '../stores/main';
import { Label } from '@/components/ui/label';

const selectedBuddy = ref('');

const store = useAppStore();

const emit = defineEmits(['select']);

const props = defineProps({
	includeAi: {
		type: Boolean,
		default: true,
	},
});

const startChat = (id: string | null) => {
	emit('select', id);
};
</script>

<template>
	<div class="flex items-center space-x-2 justify-around w-full">
		<Label>Chat with:</Label>
		<Button v-if="includeAi" @click="startChat('ai')"> Assistant </Button>
		<Select
			v-if="store.buddies.length > 0"
			class="my-2"
			@update:modelValue="
				(id) => {
					startChat(id);
					selectedBuddy = '';
				}
			"
			v-model="selectedBuddy"
		>
			<SelectTrigger>
				<SelectValue placeholder="Buddy" />
			</SelectTrigger>
			<SelectContent>
				<SelectLabel>Buddies</SelectLabel>
				<SelectGroup>
					<SelectItem
						v-for="buddy in store.buddies"
						:key="buddy.id"
						:value="buddy.id"
						@click="startChat(buddy.id)"
					>
						{{ buddy.name }}
					</SelectItem>
				</SelectGroup>
			</SelectContent>
		</Select>
	</div>
</template>
