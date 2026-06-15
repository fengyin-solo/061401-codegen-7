<script setup lang="ts">
import { computed } from 'vue'
import type { Companion } from '@/types/game'

interface Props {
  show: boolean
  finalTurn: number
  highScore: number
  isNewRecord: boolean
  companions: Companion[]
}

const props = defineProps<Props>()

const emit = defineEmits<{
  restart: []
}>()

const hasCompanions = computed(() => props.companions.length > 0)

const companionJourney = computed(() => {
  return props.companions.map(c => ({
    ...c,
    turnsTogether: props.finalTurn - c.metTurn,
  }))
})
</script>

<template>
  <Teleport to="body">
    <Transition name="modal">
      <div
        v-if="show"
        class="fixed inset-0 z-50 flex items-center justify-center p-4"
      >
        <div class="absolute inset-0 bg-black/70 backdrop-blur-sm"></div>
        <div class="relative bg-game-card rounded-3xl p-8 max-w-md w-full border border-game-border shadow-2xl animate-slide-up max-h-[90vh] overflow-y-auto">
          <div class="text-center">
            <div class="text-6xl mb-4">💀</div>
            <h2 class="text-3xl font-bold text-white mb-2">游戏结束</h2>
            <p class="text-gray-400 mb-6">你没能在荒野中生存下来...</p>

            <div class="bg-gray-800/50 rounded-2xl p-5 mb-6 space-y-3">
              <div class="flex justify-between items-center">
                <span class="text-gray-400">生存回合</span>
                <span class="text-2xl font-bold text-white">{{ finalTurn }}</span>
              </div>
              <div class="border-t border-gray-700"></div>
              <div class="flex justify-between items-center">
                <span class="text-gray-400">最高纪录</span>
                <span class="text-xl font-bold" :class="isNewRecord ? 'text-yellow-400' : 'text-gray-300'">
                  {{ highScore }}
                  <span v-if="isNewRecord" class="text-sm ml-1">🏆 新纪录！</span>
                </span>
              </div>
            </div>

            <div v-if="hasCompanions" class="bg-purple-900/20 rounded-2xl p-5 mb-6 border border-purple-800/30 text-left">
              <h3 class="text-lg font-bold text-white mb-3 flex items-center gap-2 justify-center">
                <span>🤝</span>
                <span>同行的伙伴</span>
              </h3>
              <div class="space-y-3">
                <div
                  v-for="companion in companionJourney"
                  :key="companion.id"
                  class="bg-gray-800/60 rounded-xl p-3 border border-gray-700/50"
                >
                  <div class="flex items-center justify-between mb-1">
                    <div class="flex items-center gap-2">
                      <span class="text-xl">{{ companion.icon }}</span>
                      <span class="text-white font-semibold text-sm">{{ companion.name }}</span>
                    </div>
                    <span class="text-purple-300 text-xs">{{ companion.turnsTogether }} 回合陪伴</span>
                  </div>
                  <p class="text-gray-400 text-xs mb-1">{{ companion.description }}</p>
                  <p class="text-green-400 text-xs">✨ {{ companion.bonusDescription }}</p>
                </div>
              </div>
              <div class="mt-3 text-center">
                <p class="text-purple-300 text-sm italic">
                  在荒野中，有伙伴同行是一种幸运...
                </p>
              </div>
            </div>

            <div v-else class="bg-gray-800/30 rounded-2xl p-5 mb-6 border border-gray-700/30">
              <p class="text-gray-500 text-sm">这一路，你独自走过了荒野...</p>
            </div>

            <button
              @click="emit('restart')"
              class="w-full py-4 px-6 bg-gradient-to-r from-green-600 to-emerald-600 hover:from-green-500 hover:to-emerald-500 text-white font-bold text-lg rounded-xl transition-all duration-200 hover:scale-[1.02] active:scale-[0.98] shadow-lg hover:shadow-green-500/25"
            >
              🔄 重新开始
            </button>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.modal-enter-active,
.modal-leave-active {
  transition: all 0.3s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .relative,
.modal-leave-to .relative {
  transform: scale(0.9);
}
</style>
