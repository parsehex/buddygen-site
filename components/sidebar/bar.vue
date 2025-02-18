<script setup lang="ts">
import { ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import { Tabs, TabsContent, TabsList, TabsTrigger } from '@/components/ui/tabs';
import { ScrollArea } from '@/components/ui/scroll-area';
import { useAppStore } from '@/stores/main';
import ThreadsList from './ThreadsList.vue';
import BuddyList from './BuddyList.vue';
import SettingsPanel from './settings/SettingsPanel.vue';
import ColorMode from './ColorMode.vue';
import { useToast } from '@/components/ui/toast';
import type { BuddyVersionMerged } from '@/lib/api/types-db';
// import { api } from '@/lib/api';
import BuddySelect from '../BuddySelect.vue';

const { toast } = useToast();

const store = useAppStore();
const route = useRoute();

const modelValue = ref(route.path.includes('/buddy') ? 'buddy' : 'chat');

watch(
	() => route.path,
	(path) => {
		if (path.includes('/buddy')) {
			modelValue.value = 'buddy';
		} else {
			modelValue.value = 'chat';
		}
	}
);

const selectedBuddy = ref('');

const doCreateThread = async () => {
	let name = '';
	let mode = '' as 'persona' | 'custom';
	let buddy_id = '';
	console.log('selected buddy', selectedBuddy.value);
	let errorMsg = '';
	const buddy = store.buddies.find(
		(buddy: BuddyVersionMerged) => buddy.id === selectedBuddy.value
	);
	if (buddy) {
		name = `Chat with ${buddy.name}`;
		mode = 'persona';
		buddy_id = buddy.id;
	} else if (selectedBuddy.value === 'ai') {
		name = 'Chat with AI';
		mode = 'custom';
	} else {
		errorMsg = 'Could not find buddy' + selectedBuddy.value;
	}
	if (errorMsg) {
		toast({
			variant: 'destructive',
			description: errorMsg,
		});
		return;
	}

	const options = {
		name,
		mode,
	} as any;
	if (mode === 'persona') {
		options.persona_id = buddy_id;
	}

	// const newThread = await api.thread.createOne(options);
	// await store.updateThreads();
	// router.push(`/chat/${newThread.id}`);
};

watch(
	() => store.buddies,
	(newVal) => {
		if (selectedBuddy.value) {
			const buddy = newVal.find(
				(buddy: BuddyVersionMerged) => buddy.id === selectedBuddy.value
			);
			if (!buddy) {
				selectedBuddy.value = '';
			}
		}
	}
);
</script>

<template>
	<Tabs v-model:model-value="modelValue">
		<TabsList class="w-full dark:bg-gray-800 rounded-none">
			<TabsTrigger value="chat">Chat</TabsTrigger>
			<TabsTrigger value="buddy">Buddy</TabsTrigger>
			<TabsTrigger value="settings">Settings</TabsTrigger>
			<ColorMode />
		</TabsList>
		<div class="h-screen">
			<TabsContent value="chat">
				<div class="bg-background mb-1">
					<div class="flex w-full px-2 my-1 items-end">
						<BuddySelect
							@select="(id: any) => {
							selectedBuddy = id;
							doCreateThread();
						}"
						/>
					</div>
				</div>
				<ScrollArea class="h-screen">
					<ThreadsList />
				</ScrollArea>
			</TabsContent>
			<TabsContent value="buddy">
				<BuddyList />
			</TabsContent>
			<TabsContent value="settings">
				<SettingsPanel />
			</TabsContent>
		</div>
	</Tabs>
</template>

<style></style>
