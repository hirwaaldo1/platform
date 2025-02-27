<!--
//
// Copyright © 2022-2024 Hardcore Engineering Inc.
//
// Licensed under the Eclipse Public License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License. You may
// obtain a copy of the License at https://www.eclipse.org/legal/epl-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
//
// See the License for the specific language governing permissions and
// limitations under the License.
//
-->
<script lang="ts">
  import contact from '@hcengineering/contact'
  import { Document } from '@hcengineering/document'
  import { getResource } from '@hcengineering/platform'
  import { CollaboratorEditor, HeadingsExtension, ImageUploadOptions } from '@hcengineering/text-editor-resources'
  import { AnySvelteComponent } from '@hcengineering/ui'
  import { getCollaborationUser } from '@hcengineering/view-resources'
  import { Extensions, FocusPosition } from '@tiptap/core'
  import { createEventDispatcher } from 'svelte'

  export let object: Document
  export let readonly = false
  export let boundary: HTMLElement | undefined = undefined
  export let attachFile: ImageUploadOptions['attachFile'] | undefined = undefined
  export let focusIndex = -1
  export let overflow: 'auto' | 'none' = 'none'
  export let editorAttributes: Record<string, string> = {}

  const user = getCollaborationUser()
  let userComponent: AnySvelteComponent | undefined
  void getResource(contact.component.CollaborationUserAvatar).then((component) => {
    userComponent = component
  })

  let collabEditor: CollaboratorEditor

  export function focus (position?: FocusPosition): void {
    collabEditor.focus(position)
  }

  const dispatch = createEventDispatcher()

  const handleExtensions = (): Extensions => [
    HeadingsExtension.configure({
      onChange: (headings) => {
        dispatch('headings', headings)
      }
    })
  ]
</script>

<CollaboratorEditor
  collaborativeDoc={object.description}
  objectClass={object._class}
  objectId={object._id}
  objectSpace={object.space}
  objectAttr="description"
  field="description"
  {user}
  {userComponent}
  {focusIndex}
  {readonly}
  {attachFile}
  {boundary}
  {overflow}
  {editorAttributes}
  onExtensions={handleExtensions}
  on:update
  on:open-document
  bind:this={collabEditor}
/>
