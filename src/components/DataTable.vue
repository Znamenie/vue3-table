<template>
  <div class="data-table">
    <table>
      <thead v-if="headersForRender.length">
        <tr>
          <th
            v-for="(header, index) in headersForRender"
            :key="index"
            :class="{
              sortable: header.sortable,
              none: header.sortable && header.sortType === 'none',
              desc: header.sortable && header.sortType === 'desc',
              asc: header.sortable && header.sortType === 'asc',
            }"
            @click="
              header.sortable && header.sortType
                ? updateSortField(header.value, header.sortType)
                : null
            "
          >
            <span class="header-text">
              <span class="header-text__inner">
                {{ header.text }}
              </span>
              <ArrowIcon
                v-if="header.sortable"
                :key="header.sortType ? header.sortType : 'none'"
                class="sort-icon"
                :class="{ desc: header.sortType === 'desc' }"
              />
            </span>
          </th>
        </tr>
      </thead>
      <slot v-if="ifHasBodySlot" name="body" />
      <template v-else>
        <tbody v-if="items.length && headerColumns.length">
          <tr v-for="item in itemsForRender" :key="JSON.stringify(item)">
            <td v-for="(column, i) in headerColumns" :key="i">
              <slot v-if="slots[column]" :name="column" v-bind="item" />
              <template v-else>
                {{
                  Array.isArray(item[column])
                    ? item[column].join(',')
                    : item[column]
                }}
              </template>
            </td>
          </tr>
        </tbody>
      </template>
    </table>
  </div>
</template>

<script setup>
import { computed, watch, ref, toRefs, useSlots } from 'vue';
import ArrowIcon from '@/components/icons/ArrowIcon.vue';

const props = defineProps({
  bodyFontSize: {
    type: Number,
    default: 14,
  },
  headers: {
    type: Array,
    required: true,
  },
  headerFontColor: {
    type: String,
    default: '#373737',
  },
  items: {
    type: Array,
    required: true,
  },
  searchField: {
    type: String,
    default: '',
  },
  searchValue: {
    type: String,
    default: '',
  },
  sortBy: {
    type: String,
    default: '',
  },
  sortType: {
    type: String,
    default: 'asc',
  },
});

const initClientSortOptions = () => {
  if (props.sortBy !== '') {
    return {
      sortBy: props.sortBy,
      sortDesc: props.sortType === 'desc',
    };
  }
  return null;
};

const slots = useSlots();
const { headerFontColor } = toRefs(props);

const ifHasBodySlot = computed(() => slots.body);
const fontSizePx = computed(() => `${props.bodyFontSize}px`);

const clientSortOptions = ref(initClientSortOptions());

const headersForRender = computed(() => {
  const headersSorting = props.headers.map((header) => {
    const headerSorting = header;
    if (header.sortable) headerSorting.sortType = 'none';
    if (
      clientSortOptions.value &&
      header.value === clientSortOptions.value.sortBy
    ) {
      headerSorting.sortType = clientSortOptions.value.sortDesc
        ? 'desc'
        : 'asc';
    }
    return headerSorting;
  });
  return headersSorting;
});

const itemsSearching = computed(() => {
  if (props.searchValue !== '') {
    const regex = new RegExp(props.searchValue, 'i');
    return props.items.filter((item) =>
      regex.test(
        props.searchField !== ''
          ? item[props.searchField]
          : Object.values(item).join(' ')
      )
    );
  }

  return props.items;
});

const itemsSorting = computed(() => {
  if (clientSortOptions.value === null) return itemsSearching.value;
  const { sortBy, sortDesc } = clientSortOptions.value;
  const itemsSearchingSorted = [...itemsSearching.value];
  return itemsSearchingSorted.sort((a, b) => {
    if (a[sortBy] < b[sortBy]) return sortDesc ? 1 : -1;
    if (a[sortBy] > b[sortBy]) return sortDesc ? -1 : 1;
    return 0;
  });
});

const itemsForRender = computed(() => {
  return itemsSorting.value.map((item) => {
    return { ...item };
  });
});

const headerColumns = computed(() =>
  headersForRender.value.map((header) => header.value)
);

const updateSortField = (newSortBy, oldSortType = 'none') => {
  let newSortType = null;

  if (oldSortType === 'none') {
    newSortType = 'asc';
  } else if (oldSortType === 'asc') {
    newSortType = 'desc';
  } else {
    newSortType = null;
  }

  if (newSortType === null) {
    clientSortOptions.value = null;
  } else {
    clientSortOptions.value = {
      sortBy: newSortBy,
      sortDesc: newSortType === 'desc',
    };
  }
};

watch(() => {
  console.log('clientSortOptions', clientSortOptions.value);
  console.log('itemsSearching', itemsSearching.value);
});
</script>

<style lang="scss" scoped>
.data-table {
  box-sizing: border-box;
  width: 100%;
  border: 1px solid #e0e0e0;
  position: relative;
  overflow: auto;

  table {
    width: 100%;
    background-color: #fff;
    border-spacing: 0;
  }

  thead {
    position: sticky;
    top: 0;
    z-index: 1;
    font-size: 14px;

    tr {
      height: 36px;
    }

    th {
      border-bottom: 1px solid #e0e0e0;
      color: #373737;
      position: relative;
      background-color: #fff;
      text-align: left;
      padding: 0 10px;

      .header-text {
        display: flex;
        align-items: center;

        svg {
          margin-left: 3px;
        }
      }

      &.sortable {
        cursor: pointer;
        &.none {
          &:hover {
            .sort-icon {
              opacity: 1;
            }
          }
          .sort-icon {
            opacity: 0;
            transition: opacity 0.2s ease;
            &__svg {
              path {
                fill: rgb(158 158 158);
              }
            }
          }
        }
        &.desc {
          path {
            fill: v-bind(headerFontColor);
          }
        }
      }

      .sort-icon {
        display: inline-block;
        width: v-bind(fontSizePx);
        height: v-bind(fontSizePx);
        &__svg {
          width: v-bind(fontSizePx);
          height: v-bind(fontSizePx);
        }
        &.desc {
          transform: rotate(180deg);
        }
      }
    }
  }

  tbody {
    position: relative;
    font-size: 14px;

    tr {
      height: 36px;
    }

    td {
      background-color: #fafafa;
      color: #212121;
      border-bottom: 1px solid #e0e0e0;
      text-align: left;
      padding: 0 10px;
    }
  }
}
</style>
