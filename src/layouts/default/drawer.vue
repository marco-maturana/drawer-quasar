<script>
import { openURL } from 'quasar'

export default {
  name: 'LayoutDefault',
  data () {
    return {
      menus: [
        {
            "label": "Home",
            "icon": "fas fa-home",
            "url": '/',
            "parent": 0,
            "order": 1,
            "level": 0,
            "id": 17
        },
        {
            "label": "Warehouse",
            "icon": "fas fa-dolly",
            "url": null,
            "parent": 0,
            "order": 2,
            "level": 0,
            "id": 8,
            "children": [
                {
                    "label": "Documents",
                    "icon": 'fas fa-caret-right',
                    "url": '/warehouse/documents',
                    "parent": 8,
                    "order": 1,
                    "level": 1,
                    "id": 19
                },
                {
                    "label": "Direct Receiving",
                    "icon": 'fas fa-caret-right',
                    "url": '/warehouse/direct-receiving',
                    "parent": 8,
                    "order": 2,
                    "level": 1,
                    "id": 26
                },
                {
                    "label": "Inventory Update",
                    "icon": 'fas fa-caret-right',
                    "url": '/warehouse/inventory-update',
                    "parent": 8,
                    "order": 3,
                    "level": 1,
                    "id": 24
                },
                {
                    "label": "Conference",
                    "icon": 'fas fa-caret-right',
                    "url": '/warehouse/conference',
                    "parent": 8,
                    "order": 4,
                    "level": 1,
                    "id": 25
                }
            ]
        },
        {
            "label": "Terminal",
            "icon": "fas fa-desktop",
            "url": '/terminal',
            "parent": 0,
            "order": 3,
            "level": 0,
            "id": 4
        },
        {
            "label": "Financial",
            "icon": "fas fa-hand-holding-usd",
            "url": '/financial',
            "parent": 0,
            "order": 4,
            "level": 0,
            "id": 2
        },
        {
            "label": "Card",
            "icon": "fas fa-credit-card",
            "url": null,
            "parent": 0,
            "order": 5,
            "level": 0,
            "id": 7,
            "children": [
                {
                    'label': "Reports",
                    'icon': 'fas fa-caret-right',
                    'url': null,
                    'parent': 7,
                    'order': 1,
                    'level': 1,
                    'id': 27,
                    'children': [
                        {
                            'label': "Sells - Employees",
                            'icon': 'fas fa-caret-right',
                            'url': '/card/reports/sells-employees',
                            'parent': 27,
                            'order': 1,
                            'level': 2,
                            'id': 28
                        },
                        {
                            "label": "Sells - Clients",
                            "icon": 'fas fa-caret-right',
                            "url": '/card/reports/sells-clients',
                            "parent": 27,
                            "order": 2,
                            "level": 2,
                            "id": 29
                        }
                    ]
                }
            ]
        },
        {
            "label": "POS Front",
            "icon": "fas fa-id-card-alt",
            "url": '/pos-front',
            "parent": 0,
            "order": 6,
            "level": 0,
            "id": 6
        },
        {
            "label": "Clothing Store",
            "icon": "fas fa-shopping-bag",
            "url": '/clothing-store',
            "parent": 0,
            "order": 7,
            "level": 0,
            "id": 5
        },
        {
            "label": "Utilities",
            "icon": "fas fa-wrench",
            "url": '/utilities',
            "parent": 0,
            "order": 8,
            "level": 0,
            "id": 3
        },
        {
            "label": "Configurations",
            "icon": "fas fa-cogs",
            "url": '/configurations',
            "parent": 0,
            "order": 9,
            "level": 0,
            "id": 16
        }
      ],
      filtered: [ ],
      enabled: true
    }
  },
  render (createElement) {
    let layoutDrawer = createElement('q-layout-drawer', {
      props: {
        value: true
      },
    })

    let list = createElement('q-list', {
        props: {
          noBorder: true,
          link: true
        },
      },[
        this.generateHeader(createElement)
      ])
    
    this.filtered.forEach(menu => {
      if (menu.children) {
        list.componentOptions.children.push(this.generateGroupMenu(createElement, menu))
      } else {
        list.componentOptions.children.push(this.generateMenu(createElement, menu))
      }
    });

    layoutDrawer.componentOptions.children = layoutDrawer.componentOptions.children || [ ]
    layoutDrawer.componentOptions.children.push(list)

    return layoutDrawer
  },
  methods: {
    generateGroupMenu (createElement, menu) {
      let props = {
        icon: (menu.icon) ? menu.icon : undefined,
        label: menu.label,
        link: true,
        separator: true
      }

      let collapsible = createElement('q-collapsible', { props: props })

      setTimeout(() => {
        if (menu.opened)
          collapsible.componentInstance.show()
        else
          collapsible.componentInstance.hide()
      }, 500)

      collapsible.componentOptions.children = [ ]

      menu.children.forEach(child => {
        if (child.children) {
          collapsible.componentOptions.children.push(this.generateGroupMenu(createElement, child))
        } else {
          collapsible.componentOptions.children.push(this.generateMenu(createElement, child))
        }
      });

      return collapsible
    },
    generateHeader (createElement) {
      let header = createElement('q-list-header', [
        createElement('q-search', {
          on: {
            input: this.search
          },
          props: {
            placeholder: 'Search the menu',
            value: this.filter
          },
        })
      ])

      return header
    },
    generateMenu (createElement, menu) {
      let item = createElement('q-item', {
        props: {
          to: menu.url,
          exact: true,
          separator: true,
          inset: true
        },
      })

      item.componentOptions.children = [ ]

      if (menu.icon) {
        const itemSide = createElement('q-item-side', [
          createElement('q-icon', {
            props: {
              color: 'primary',
              name: menu.icon,
              size: '24px'
            }
          })
        ])

        item.componentOptions.children.push(itemSide)
      }

      const itemMain = createElement('q-item-main', {
          props: {
            label: menu.label
          }
        })
        item.componentOptions.children.push(itemMain)

      return item
    },
    filterMenu (menu, filter) {
      if (menu.label.toLowerCase().lastIndexOf(filter.toLowerCase()) >= 0) {
        return menu
      } else {
        if (menu.children) {
          let filteredChildren = [ ]

          menu.children.forEach(child => {
            const filteredMenu = this.filterMenu(child, filter)

            if (filteredMenu) filteredChildren.push(filteredMenu)
          })

          menu.children = filteredChildren

          if ((filteredChildren.length > 0) ) {
            menu.opened = true
             return menu
          }
        }
      }

      return null
    },
    clone (object) {
      return JSON.parse(JSON.stringify(object))
    },
    search (filter) {
      if (filter) {
        this.filtered = [ ]

        this.menus.forEach(menu => {
          const filteredMenu = this.filterMenu(this.clone(menu), filter)

          if (filteredMenu) this.filtered.push(filteredMenu)
        })        
      } else this.filtered = this.clone(this.menus)

      this.$forceUpdate()
    }
  },
  created () {
    this.filtered = this.clone(this.menus)
  }
}
</script>

<style lang='stylus'>
  .q-collapsible .q-item-side-left .q-icon {
    color: #027be3
  }
  .q-collapsible-sub-item .q-item-side-left {
    margin-right : -25px
  }
</style>