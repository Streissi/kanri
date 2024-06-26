<!-- SPDX-FileCopyrightText: Copyright (c) 2022-2024 trobonox <hello@trobo.dev> -->
<!-- -->
<!-- SPDX-License-Identifier: GPL-3.0-or-later -->
<!--
Kanri is an offline Kanban board app made using Tauri and Nuxt.
Copyright (C) 2022-2024 trobonox <hello@trobo.dev>

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <https://www.gnu.org/licenses/>. -->

<template>
    <nav
        :class="zIndexDown ? '' : 'z-50'"
        class="border-elevation-1 bg-sidebar mr-8 flex h-screen flex-col items-center justify-between overflow-hidden border-r-2 px-8 pb-6 pt-5 shadow-md"
    >
        <ModalNewBoard
            v-show="newBoardModalVisible"
            @closeModal="newBoardModalVisible = false"
        />
        <Teleport to=".default-layout">
            <ModalHelp
                v-show="helpModalVisible"
                @closeModal="helpModalVisible = false"
            />
        </Teleport>

        <section
            id="items-top"
            class="flex flex-col items-center gap-4"
        >
            <div
                id="logo"
                class="flex flex-row rounded-md"
            >
                <IconKanri
                    class="text-accent-logo-icon size-9 pl-1"
                    @click="$router.push('/')"
                />
            </div>
            <button
                v-if="showAddButton"
                v-tooltip.left-start="'Home'"
                class="bg-elevation-2-hover transition-button rounded-md p-2"
                @click="$router.push('/')"
            >
                <PhHouse class="size-7" />
            </button>
            <button
                v-else
                v-tooltip.left-start="'Back'"
                class="bg-elevation-2-hover transition-button rounded-md p-2"
                @click="$router.go(-1)"
            >
                <PhArrowBendUpLeft class="size-7" />
            </button>
            <button
                v-if="showAddButton"
                v-tooltip.left-start="'Create a new board'"
                class="bg-elevation-2-hover transition-button rounded-md p-2"
                @click="newBoardModalVisible = true"
            >
                <IconPhPlusCircleDuotone class="text-accent size-7" />
            </button>
        </section>

        <section
            id="icons-bottom"
            class="flex flex-col items-center gap-4"
        >
            <nuxt-link
                v-tooltip.left-start="'Tags'"
                to="/tags"
            >
                <div class="bg-elevation-2-hover transition-button rounded-md p-2">
                    <PhHash class="size-7" />
                </div>
            </nuxt-link>
            <nuxt-link
                v-tooltip.left-start="'Import/Export'"
                to="/import"
            >
                <div class="bg-elevation-2-hover transition-button rounded-md p-2">
                    <PhArrowsLeftRight class="size-7" />
                </div>
            </nuxt-link>
            <button
                v-tooltip.left-start="'Help'"
                class="bg-elevation-2-hover transition-button rounded-md p-2"
                @click="helpModalVisible = true"
            >
                <PhQuestion class="size-7" />
            </button>
            <nuxt-link
                v-tooltip.left-start="'Settings'"
                to="/settings"
            >
                <div class="bg-elevation-2-hover transition-button rounded-md p-2">
                    <PhGearSix class="size-7" />
                </div>
            </nuxt-link>
        </section>
    </nav>
</template>

<script setup lang="ts">
import emitter from "@/utils/emitter";
import { PhArrowBendUpLeft, PhHash, PhHouse } from "@phosphor-icons/vue";
import { PhArrowsLeftRight, PhGearSix, PhQuestion } from "@phosphor-icons/vue";

const store = useTauriStore().store;

const helpModalVisible = ref(false);
const newBoardModalVisible = ref(false);

const zIndexDown = ref(false);
const showAddButton = ref(true);

const savedColors: Ref<any> = ref(null);

onMounted(async () => {
    document.addEventListener("keydown", keyDownListener);

    savedColors.value = await store.get("colors");

    emitter.on("updateColors", async () => {
        savedColors.value = await store.get("colors");
    })

    emitter.on("zIndexDown", () => {
        zIndexDown.value = true;
    });

    emitter.on("zIndexBack", () => {
        zIndexDown.value = false;
    });

    emitter.on("openKanbanPage", () => {
        showAddButton.value = false;
    });

    emitter.on("closeKanbanPage", () => {
        showAddButton.value = true;
    });

    emitter.on("showSidebarBackArrow", () => {
        showAddButton.value = false;
    });

    emitter.on("hideSidebarBackArrow", () => {
        showAddButton.value = true;
    });
});

onBeforeUnmount(() => {
    document.removeEventListener("keydown", keyDownListener);
});

const keyDownListener = (e: KeyboardEvent) => {
    if (e.key === "F1") {
        helpModalVisible.value = true;
        return;
    }
}
</script>

<style scoped>
.bg-sidebar {
    background: radial-gradient(circle at top left, var(--elevation-1) 10%, transparent);
}
</style>
