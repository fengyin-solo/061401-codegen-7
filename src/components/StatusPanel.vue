<script setup lang="ts">
import { computed } from 'vue'
import type { Companion, ActionEffect } from '@/types/game'

interface StatItem {
  label: string
  value: number
  max: number
  icon: string
  color: string
  barColor: string
  isReverse?: boolean
}

interface Props {
  health: number
  hunger: number
  thirst: number
  wood: number
  stone: number
  companions: Companion[]
  totalBonuses: ActionEffect
}

const props = defineProps<Props>()

const stats = computed<StatItem[]>(() => [
  {
    label: '生命值',
    value: props.health,
    max: 100,
    icon: '❤️',
    color: 'text-red-400',
    barColor: 'bg-red-500',
  },
  {
    label: '饥饿值',
    value: props.hunger,
    max: 100,
    icon: '🍖',
    color: 'text-orange-400',
    barColor: 'bg-orange-500',
    isReverse: true,
  },
  {
    label: '口渴值',
    value: props.thirst,
    max: 100,
    icon: '💧',
    color: 'text-blue-400',
    barColor: 'bg-blue-500',
    isReverse: true,
  },
  {
    label: '木材',
    value: props.wood,
    max: 100,
    icon: '🪵',
    color: 'text-amber-600',
    barColor: 'bg-amber-600',
  },
  {
    label: '石头',
    value: props.stone,
    max: 100,
    icon: '🪨',
    color: 'text-gray-400',
    barColor: 'bg-gray-400',
  },
])

const hasCompanions = computed(() => props.companions.length > 0)

function getBonusTag(bonus: ActionEffect): string {
  const parts: string[] = []
  if (bonus.health && bonus.health > 0) parts.push(`❤️+${bonus.health}`)
  if (bonus.health && bonus.health < 0) parts.push(`❤️${bonus.health}`)
  if (bonus.hunger && bonus.hunger < 0) parts.push(`🍖${bonus.hunger}`)
  if (bonus.hunger && bonus.hunger > 0) parts.push(`🍖+${bonus.hunger}`)
  if (bonus.thirst && bonus.thirst < 0) parts.push(`💧${bonus.thirst}`)
  if (bonus.thirst && bonus.thirst > 0) parts.push(`💧+${bonus.thirst}`)
  if (bonus.wood && bonus.wood > 0) parts.push(`🪵+${bonus.wood}`)
  if (bonus.stone && bonus.stone > 0) parts.push(`🪨+${bonus.stone}`)
  return parts.join(' ')
}

function getBarWidth(value: number, max: number): string {
  const percent = Math.max(0, Math.min(100, (value / max) * 100))
  return `${percent}%`
}

function isDanger(value: number, max: number, isReverse?: boolean): boolean {
  const percent = (value / max) * 100
  if (isReverse) {
    return percent >= 80
  }
  return percent <= 20
}
</script>

<template>
  <div class="bg-game-card rounded-2xl p-6 border border-game-border shadow-xl">
    <h2 class="text-xl font-bold text-white mb-5 flex items-center gap-2">
      <span>📊</span>
      <span>生存状态</span>
    </h2>
    <div class="space-y-4">
      <div
        v-for="stat in stats"
        :key="stat.label"
        class="group"
      >
        <div class="flex items-center justify-between mb-1.5">
          <div class="flex items-center gap-2">
            <span class="text-lg">{{ stat.icon }}</span>
            <span :class="[stat.color, 'font-medium text-sm']">{{ stat.label }}</span>
          </div>
          <span
            :class="[
              stat.color,
              'font-bold text-sm tabular-nums',
              isDanger(stat.value, stat.max, stat.isReverse) ? 'animate-pulse-soft' : '',
            ]"
          >
            {{ Math.round(stat.value) }}
          </span>
        </div>
        <div class="h-2.5 bg-gray-800 rounded-full overflow-hidden">
          <div
            :class="[stat.barColor, 'h-full rounded-full transition-all duration-300 ease-out']"
            :style="{ width: getBarWidth(stat.value, stat.max) }"
          ></div>
        </div>
      </div>
    </div>

    <div v-if="hasCompanions" class="mt-5 pt-5 border-t border-gray-700">
      <h3 class="text-lg font-bold text-white mb-3 flex items-center gap-2">
        <span>🤝</span>
        <span>救援伙伴</span>
        <span class="text-xs bg-purple-600/50 text-purple-200 px-2 py-0.5 rounded-full">{{ companions.length }}</span>
      </h3>
      <div class="space-y-2.5">
        <div
          v-for="companion in companions"
          :key="companion.id"
          class="bg-gray-800/60 rounded-xl p-3 border border-gray-700/50"
        >
          <div class="flex items-center gap-2 mb-1">
            <span class="text-xl">{{ companion.icon }}</span>
            <span class="text-white font-semibold text-sm">{{ companion.name }}</span>
          </div>
          <p class="text-gray-400 text-xs mb-1.5">{{ companion.description }}</p>
          <div class="flex items-center gap-1.5">
            <span class="text-xs bg-green-900/40 text-green-300 px-2 py-0.5 rounded-full border border-green-800/50">
              {{ companion.bonusDescription }}
            </span>
          </div>
          <p class="text-gray-500 text-xs mt-1.5">第 {{ companion.metTurn }} 回合结识</p>
        </div>
      </div>

      <div v-if="Object.keys(totalBonuses).length > 0" class="mt-3 bg-emerald-900/20 rounded-lg p-2.5 border border-emerald-800/30">
        <p class="text-emerald-300 text-xs font-medium">每回合总加成：{{ getBonusTag(totalBonuses) }}</p>
      </div>
    </div>

    <div v-else class="mt-5 pt-5 border-t border-gray-700">
      <h3 class="text-lg font-bold text-white mb-3 flex items-center gap-2">
        <span>🤝</span>
        <span>救援伙伴</span>
      </h3>
      <p class="text-gray-500 text-sm text-center py-3">探索荒野，也许会遇到愿意同行的伙伴...</p>
    </div>
  </div>
</template>
