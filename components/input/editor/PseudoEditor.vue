<script>
import SimpleEditor from '@/components/input/editor/SimpleEditor.vue'
import EditorToolbar from '@/components/input/editor/EditorToolbar.vue'
import { fileToElement } from '@/fixture/common/fileManage.js'
import gist from '@/mixins/input/gist.js'
import elements from '@/mixins/input/editor/elements'

export default {
  mixins: [gist, elements],
  data() {
    return {
      menuActive: false,
      files: [],
    }
  },
  render(h) {
    const self = this
    return h(
      'div',
      {
        class: {
          'editor-container': true,
        },
      },
      [
        h(
          'v-menu',
          {
            props: {
              contentClass: 'menu-content',
              value: self.menuActive,
              closeOnClick: true,
              closeOnContentClick: false,
              absolute: true,
              top: true,
              'offset-y': true,
              'offset-x': true,
            },
            on: {
              input: (val) => (self.menuActive = val),
            },
            scopedSlots: {
              activator: ({ on }) => {
                return h(SimpleEditor, {
                  ref: 'editor',
                  on,
                })
              },
            },
          },
          [
            h(EditorToolbar, {
              props: {
                cmds: ['selectAll', 'bold', 'heading_2', 'heading_3'],
              },
              on: {
                fileChange: ({ file }) => {
                  self.files.push(file)
                  const { parentElement } = this.getAnchorElements()
                  const newLine = document.createElement('p')
                  fileToElement(file).then((element) => {
                    newLine.appendChild(element)
                    parentElement.after(newLine)
                  })
                },
                addSection: () => {
                  const { parentElement, node } = this.getAnchorElements()
                  parentElement.setAttribute('id', node.textContent)
                  self.$refs.editor.trggerUpdateSections()
                },
              },
            }),
            self.genGistForm(h),
          ]
        ),
      ]
    )
  },
}
</script>
