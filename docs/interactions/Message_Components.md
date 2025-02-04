# Message Components

Message components—we'll call them "components" moving forward—are a framework for adding interactive elements to the messages your app or bot sends. They're accessible, customizable, and easy to use.

There are several different types of components; this documentation will outline the basics of this new framework and each example.

## What is a Component

Components are a field on the [message object](#DOCS_RESOURCES_MESSAGE/message-object), so you can use them whether you're sending messages or responding to a [slash command](#DOCS_INTERACTIONS_APPLICATION_COMMANDS/) or other interaction.

### Component Object

###### Component Example

```json
{
    "content": "This is a message with components",
    "components": [
        {
            "id": 1,
            "type": 1,
            "components": []
        }
    ]
}
```

###### Base Component Structure
| Field      | Type    | Description                                                                                 |
|------------|---------|---------------------------------------------------------------------------------------------|
| id?\* \*\* | integer | Unique identifier for the component; auto populated through increment if not provided.      |
| type       | integer | [Type](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types) of component |

\* `id` is optional when sending a message, but will always be present when received.
\*\* Auto population starts at `1` and you are not able to define `0` as the id.

###### Component Types

| Type | Name                    | Description                                       | Version\* | Top Level | Interactive\*\* |
|------|-------------------------|---------------------------------------------------|-----------|-----------|-----------------|
| 1    | Action Row              | Container for other components                    | 1, 2      | Yes       | No              |
| 2    | Button                  | Button object                                     | 1, 2      | No        | Yes             |
| 3    | String Select           | Select menu for picking from defined text options | 1, 2      | No        | Yes             |
| 4    | Text Input              | Text input object                                 | 1, 2      | No        | Yes             |
| 5    | User Select             | Select menu for users                             | 1, 2      | No        | Yes             |
| 6    | Role Select             | Select menu for roles                             | 1, 2      | No        | Yes             |
| 7    | Mentionable Select      | Select menu for mentionables (users *and* roles)  | 1, 2      | No        | Yes             |
| 8    | Channel Select          | Select menu for channels                          | 1, 2      | No        | Yes             |
| 9    | Section                 | Section component                                 | 2         | Yes       | No              |
| 10   | Text Display            | Text display component                            | 2         | Yes       | No              |
| 11   | Thumbnail               | Thumbnail component for a section component       | 2         | No        | No              |
| 12   | Media Gallery           | Media gallery component                           | 2         | Yes       | No              |
| 13   | File                    | File display component                            | 2         | Yes       | No              |
| 14   | Separator               | Separator component                               | 2         | Yes       | No              |
| 16   | Content Inventory Entry | Cannot be used by bots                            | 1, 2      | Yes       | No              |
| 17   | Container               | Container for other components                    | 2         | Yes       | No              |

\* Using `v2` components requires setting the [message flag](#DOCS_RESOURCES_MESSAGE/message-object-message-flags) `1 << 15`. Setting the flag will disable the ability to set the `content` and `embeds` field and therefore will throw `Invalid Form Body` if either is sent.

\*\* Interactive components send interactions and require a `custom_id` field.

The structure of each component type is described in detail below.

## Action Rows

An Action Row is a non-interactive container component for other types of components. It has a `type: 1` and a sub-array of `components` of other types.

- You can have up to 5 Action Rows per message
- An Action Row cannot contain another Action Row

### Action Row Object

###### Action Row Example

```json
{
    "content": "This is a message with components",
    "components": [
        {
            "id": 1,
            "type": 1,
            "components": [
                {
                    "id": 2,
                    "type": 2,
                    "label": "Click me!",
                    "style": 1,
                    "custom_id": "click_one"
                }
            ]

        }
    ]
}
```

###### Action Row Structure

| Field      | Type                                                                                 | Description                                                                                                                            |
|------------|--------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| id?        | integer                                                                              | Unique identifier for the component; auto populated through increment if not provided.                                                 |
| type       | integer                                                                              | `1` for an action row                                                                                                                  |
| components | array of [component objects](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object) | Can be of [type](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types) `2`, `3`, `4`, `5`, `6`, `7` or `8`; max of 5 |

## Responding to a Component Interaction

Responding to a user interacting with a component is the same as other interaction types, like application commands. You can simply ACK the request, send a followup message, or edit the original message to something new. Check out [Responding to An Interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/responding-to-an-interaction) and [interaction response](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-response-object) for more.

## Custom ID

Interactive components, must have a `custom_id` field. This field is defined by the developer when sending the component payload, and is returned in the interaction payload sent when a user interacts with the component. For example, if you set `custom_id: click_me` on a button, you'll receive an interaction containing `custom_id: click_me` when a user clicks that button.

`custom_id` must be unique per component; multiple buttons on the same message must not share the same `custom_id`. This field is a string of max 100 characters, and can be used flexibly to maintain state or pass through other important data.

## Buttons

Buttons are interactive components that render in messages. They can be clicked by users, and send an [interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-object) to your app when clicked.

- Buttons must be sent inside an Action Row
- An Action Row can contain up to 5 buttons
- An Action Row containing buttons cannot also contain any select menu components

### Button Object

###### Button Structure

| Field      | Type                                                | Description                                                                                                         |
|------------|-----------------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
| id?        | integer                                             | Unique identifier for the component; auto populated through increment if not provided.                              |
| type       | integer                                             | `2` for a button                                                                                                    |
| style      | integer                                             | A [button style](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/button-object-button-styles)                                 |
| label?     | string                                              | Text that appears on the button; max 80 characters                                                                  |
| emoji?     | partial [emoji](#DOCS_RESOURCES_EMOJI/emoji-object) | `name`, `id`, and `animated`                                                                                        |
| custom_id? | string                                              | Developer-defined identifier for the button; max 100 characters                                                     |
| sku_id?    | snowflake                                           | Identifier for a purchasable [SKU](#DOCS_RESOURCES_SKU/sku-object), only available when using premium-style buttons |
| url?       | string                                              | URL for link-style buttons                                                                                          |
| disabled?  | boolean                                             | Whether the button is disabled (defaults to `false`)                                                                |

Buttons come in a variety of styles to convey different types of actions. These styles also define what fields are valid for a button.

- Non-link and Non-premium buttons **must** have a `custom_id`, and cannot have a `url` or a `sku_id`.
- Link buttons **must** have a `url`, and cannot have a `custom_id`
- Link buttons do not send an [interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-object) to your app when clicked
- Premium buttons **must** contain a `sku_id`, and cannot have a `custom_id`, `label`, `url`, or `emoji`.
- Premium buttons do not send an [interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-object) to your app when clicked

###### Button Styles

| Name      | Value | Color                    | Required Field |
|-----------|-------|--------------------------|----------------|
| Primary   | 1     | blurple                  | `custom_id`    |
| Secondary | 2     | grey                     | `custom_id`    |
| Success   | 3     | green                    | `custom_id`    |
| Danger    | 4     | red                      | `custom_id`    |
| Link      | 5     | grey, navigates to a URL | `url`          |
| Premium   | 6     | blurple                  | `sku_id`       |

![An image showing the different button styles](button-styles.png)

When a user clicks on a button, your app will receive an [interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-object) including the message the button was on:

### Button Design Guidelines

###### General Button copy

- 34 characters max with icon or emoji.
- 38 characters max without icon or emoji.
- Keep text concise and to the point.
- Use clear and easily understandable language. Avoid jargon or overly technical terms.
- Use verbs that indicate the outcome of the action.
- Maintain consistency in language and tone across buttons.
- Anticipate the need for translation, test for expansion or contraction in different languages.

###### Multiple Buttons

Use different button styles to create a hierarchy. Use only one `Primary` button per group.

![Example showing one primary button per button group](multiple-buttons-example-1.png)

If there are multiple buttons of equal significance, use the `Secondary` button style for all buttons

![Example showing multiple buttons in a group with equal significance](multiple-buttons-example-2.png)

###### Premium Buttons

Premium buttons will automatically have:

- Shop Icon
- Blurple Background
- SKU name
- SKU price
- 34 character max for this button. Longer titles will be truncated

![A premium button](premium-button.png)

### Component Interaction Object

###### Sample Component Interaction

```json
{
    "version": 1,
    "type": 3,
    "token": "unique_interaction_token",
    "message": {
        "type": 0,
        "tts": false,
        "timestamp": "2021-05-19T02:12:51.710000+00:00",
        "pinned": false,
        "mentions": [],
        "mention_roles": [],
        "mention_everyone": false,
        "id": "844397162624450620",
        "flags": 0,
        "embeds": [],
        "edited_timestamp": null,
        "content": "This is a message with components.",
        "components": [
            {
                "id": 1,
                "type": 1,
                "components": [
                    {
                        "id": 2,
                        "type": 2,
                        "label": "Click me!",
                        "style": 1,
                        "custom_id": "click_one"
                    }
                ]
            }
        ],
        "channel_id": "345626669114982402",
        "author": {
            "username": "Mason",
            "public_flags": 131141,
            "id": "53908232506183680",
            "discriminator": "1337",
            "avatar": "a_d5efa99b3eeaa7dd43acca82f5692432"
        },
        "attachments": []
    },
    "member": {
        "user": {
            "username": "Mason",
            "public_flags": 131141,
            "id": "53908232506183680",
            "discriminator": "1337",
            "avatar": "a_d5efa99b3eeaa7dd43acca82f5692432"
        },
        "roles": [
            "290926798626357999"
        ],
        "premium_since": null,
        "permissions": "17179869183",
        "pending": false,
        "nick": null,
        "mute": false,
        "joined_at": "2017-03-13T19:19:14.040000+00:00",
        "is_pending": false,
        "deaf": false,
        "avatar": null
    },
    "id": "846462639134605312",
    "guild_id": "290926798626357999",
    "data": {
        "id": 2,
        "custom_id": "click_one",
        "component_type": 2
    },
    "channel_id": "345626669114982999",
    "application_id": "290926444748734465"
}
```

## Select Menus

Select menus are interactive components that allow users to select one or more options from a dropdown list in messages. On desktop, clicking on a select menu opens a dropdown-style UI; on mobile, tapping a select menu opens up a half-sheet with the options.

![A role select component on desktop](desktop-role-select-menu.png)

Select menus support single-select and multi-select behavior, meaning you can prompt a user to choose just one item from a list, or multiple. When a user finishes making their choice(s) by clicking out of the dropdown or closing the half-sheet, your app will receive an [interaction](#DOCS_INTERACTIONS_RECEIVING_AND_RESPONDING/interaction-object-interaction-structure).

- Select menus must be sent inside an Action Row
- An Action Row can contain only one select menu
- An Action Row containing a select menu cannot also contain buttons

### Select Menu Types

There are 5 different [select menu components](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types) that can be included in Action Rows.

The string select menu (type `3`) is the *only* select type that allows (and *requires*) apps to define the `options` that appear in the dropdown list. The other 4 select menu components (users, roles, mentionables, and channels) are auto-populated with options corresponding to the resource type—similar to [command option types](#DOCS_INTERACTIONS_APPLICATION_COMMANDS/application-command-object-application-command-option-type).

In addition to the `values` array in all [select menu interaction payloads](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/select-menu-object-select-menu-interaction), auto-populated select menu components (user, role, mentionable, and channel) also include an additional [`resolved` object](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/select-menu-object-select-menu-resolved-object) that provides additional details about the user's selected resource.

The payloads for the select menu components are detailed in the [select menu structure](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/select-menu-object-select-menu-structure).

###### Select Menu Example

```json
// This is a message
{
    "content": "Mason is looking for new arena partners. What classes do you play?",
    "components": [
        {
            "id": 1,
            "type": 1,
            "components": [
                {
                    "id": 2,
                    "type": 3,
                    "custom_id": "class_select_1",
                    "options":[
                        {
                            "label": "Rogue",
                            "value": "rogue",
                            "description": "Sneak n stab",
                            "emoji": {
                                "name": "rogue",
                                "id": "625891304148303894"
                            }
                        },
                        {
                            "label": "Mage",
                            "value": "mage",
                            "description": "Turn 'em into a sheep",
                            "emoji": {
                                "name": "mage",
                                "id": "625891304081063986"
                            }
                        },
                        {
                            "label": "Priest",
                            "value": "priest",
                            "description": "You get heals when I'm done doing damage",
                            "emoji": {
                                "name": "priest",
                                "id": "625891303795982337"
                            }
                        }
                    ],
                    "placeholder": "Choose a class",
                    "min_values": 1,
                    "max_values": 3
                }
            ]
        }
    ]
}
```

### Select Menu Object

###### Select Menu Structure

| Field                 | Type                                                                                                                      | Description                                                                                                                                                                |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id?                   | integer                                                                                                                   | Unique identifier for the component; auto populated through increment if not provided.                                                                                     |
| type                  | integer                                                                                                                   | [Type](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types) of select menu component (text: `3`, user: `5`, role: `6`, mentionable: `7`, channels: `8`) |
| custom_id             | string                                                                                                                    | ID for the select menu; max 100 characters                                                                                                                                 |
| options?\*            | array of [select options](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/select-menu-object-select-option-structure)               | Specified choices in a select menu (only required and available for string selects (type `3`); max 25                                                                      |
| channel_types?\*\*    | array of [channel types](#DOCS_RESOURCES_CHANNEL/channel-object-channel-types)                                            | List of channel types to include in the channel select component (type `8`)                                                                                                |
| placeholder?          | string                                                                                                                    | Placeholder text if nothing is selected; max 150 characters                                                                                                                |
| default_values?\*\*\* | array of [default value objects](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/select-menu-object-select-default-value-structure) | List of default values for auto-populated select menu components; number of default values must be in the range defined by `min_values` and `max_values`                   |
| min_values?           | integer                                                                                                                   | Minimum number of items that must be chosen (defaults to 1); min 0, max 25                                                                                                 |
| max_values?           | integer                                                                                                                   | Maximum number of items that can be chosen (defaults to 1); max 25                                                                                                         |
| disabled?             | boolean                                                                                                                   | Whether select menu is disabled (defaults to `false`)                                                                                                                      |

\* `options` is required for string select menus (component type `3`), and unavailable for all other select menu components.

\*\* `channel_types` can only be used for channel select menu components.

\*\*\* `default_values` is only available for auto-populated select menu components, which include user (`5`), role (`6`), mentionable (`7`), and channel (`8`) [components](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types).

###### Select Option Structure

| Field        | Type                                                       | Description                                              |
|--------------|------------------------------------------------------------|----------------------------------------------------------|
| label        | string                                                     | User-facing name of the option; max 100 characters       |
| value        | string                                                     | Dev-defined value of the option; max 100 characters      |
| description? | string                                                     | Additional description of the option; max 100 characters |
| emoji?       | partial [emoji](#DOCS_RESOURCES_EMOJI/emoji-object) object | `id`, `name`, and `animated`                             |
| default?     | boolean                                                    | Will show this option as selected by default             |

###### Select Default Value Structure

| Field | Type      | Description                                                                   |
|-------|-----------|-------------------------------------------------------------------------------|
| id    | snowflake | ID of a user, role, or channel                                                |
| type  | string    | Type of value that `id` represents. Either `"user"`, `"role"`, or `"channel"` |

###### Select Menu Interaction

```json
{
    "application_id": "845027738276462632",
    "channel_id": "772908445358620702",
    "data": {
        "id": 2,
        "component_type": 3,
        "custom_id": "class_select_1",
        "values": [
            "mage",
            "rogue"
        ]
    },
    "guild_id": "772904309264089089",
    "id": "847587388497854464",
    "member": {
        "avatar": null,
        "deaf": false,
        "is_pending": false,
        "joined_at": "2020-11-02T19:25:47.248000+00:00",
        "mute": false,
        "nick": "Bot Man",
        "pending": false,
        "permissions": "17179869183",
        "premium_since": null,
        "roles": [
            "785609923542777878"
        ],
        "user":{
            "avatar": "a_d5efa99b3eeaa7dd43acca82f5692432",
            "discriminator": "1337",
            "id": "53908232506183680",
            "public_flags": 131141,
            "username": "Mason"
        }
    },
    "message":{
        "application_id": "845027738276462632",
        "attachments": [],
        "author": {
            "avatar": null,
            "bot": true,
            "discriminator": "5284",
            "id": "845027738276462632",
            "public_flags": 0,
            "username": "Interactions Test"
        },
        "channel_id": "772908445358620702",
        "components": [
            {
                "id": 1,
                "type": 1,
                "components": [
                    {
                        "id": 2,
                        "custom_id": "class_select_1",
                        "max_values": 1,
                        "min_values": 1,
                        "options": [
                            {
                                "description": "Sneak n stab",
                                "emoji":{
                                    "id": "625891304148303894",
                                    "name": "rogue"
                                },
                                "label": "Rogue",
                                "value": "rogue"
                            },
                            {
                                "description": "Turn 'em into a sheep",
                                "emoji":{
                                    "id": "625891304081063986",
                                    "name": "mage"
                                },
                                "label": "Mage",
                                "value": "mage"
                            },
                            {
                                "description": "You get heals when I'm done doing damage",
                                "emoji":{
                                    "id": "625891303795982337",
                                    "name": "priest"
                                },
                                "label": "Priest",
                                "value": "priest"
                            }
                        ],
                        "placeholder": "Choose a class",
                        "type": 3
                    }
                ]
            }
        ],
        "content": "Mason is looking for new arena partners. What classes do you play?",
        "edited_timestamp": null,
        "embeds": [],
        "flags": 0,
        "id": "847587334500646933",
        "interaction": {
            "id": "847587333942935632",
            "name": "dropdown",
            "type": 2,
            "user": {
                "avatar": "a_d5efa99b3eeaa7dd43acca82f5692432",
                "discriminator": "1337",
                "id": "53908232506183680",
                "public_flags": 131141,
                "username": "Mason"
            }
        },
        "mention_everyone": false,
        "mention_roles":[],
        "mentions":[],
        "pinned": false,
        "timestamp": "2021-05-27T21:29:27.956000+00:00",
        "tts": false,
        "type": 20,
        "webhook_id": "845027738276462632"
    },
    "token": "UNIQUE_TOKEN",
    "type": 3,
    "version": 1
}
```

###### Select Menu Resolved Object

The `resolved` object is included in interaction payloads for user, role, mentionable, and channel select menu components. `resolved` contains a nested object with additional details about the selected options with the key of the resource type—`users`, `roles`, `channels`, and `members`.

> info
> `members` and `users` may both be present in the `resolved` object when a user is selected (in either a user select or mentionable select).

###### Example Resolved Object

A sample `data` object (a subset of the interaction payload) for a channel select menu component:

```json
{
    "component_type": 8,
    "custom_id": "my_channel_select",
    "resolved": {
        "channels": {
            "986678954901234567": {
                "id": "986678954901234567",
                "name": "general",
                "permissions": "4398046511103",
                "type": 0
            }
        }
    },
    "values": [
        "986678954901234567"
    ]
}
```

## Text Inputs

Text inputs are an interactive component that render in modals. They can be used to collect short-form or long-form text.

When defining a text input component, you can set attributes to customize the behavior and appearance of it. However, not all attributes will be returned in the [text input interaction payload](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/text-input-object-text-input-interaction).

![A text input in a modal on desktop client](modal-desktop.png)

###### Text Input Example

```json
// This is a modal
{
    "title":"My Cool Modal",
    "custom_id":"cool_modal",
    "components": [
        {
            "id": 1,
            "type":1,
            "components":[
                {
                    "id": 2,
                    "type":4,
                    "custom_id":"name",
                    "label":"Name",
                    "style":1,
                    "min_length":1,
                    "max_length":4000,
                    "placeholder":"John",
                    "required":true
                }
            ]
        }
    ]
}
```

### Text Input Object

###### Text Input Structure

| Field        | Type    | Description                                                                                       |
|--------------|---------|---------------------------------------------------------------------------------------------------|
| id?          | integer | Unique identifier for the component; auto populated through increment if not provided.            |
| type         | integer | `4` for a text input                                                                              |
| custom_id    | string  | Developer-defined identifier for the input; max 100 characters                                    |
| style        | integer | The [Text Input Style](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/text-input-object-text-input-styles) |
| label        | string  | Label for this component; max 45 characters                                                       |
| min_length?  | integer | Minimum input length for a text input; min 0, max 4000                                            |
| max_length?  | integer | Maximum input length for a text input; min 1, max 4000                                            |
| required?    | boolean | Whether this component is required to be filled (defaults to `true`)                              |
| value?       | string  | Pre-filled value for this component; max 4000 characters                                          |
| placeholder? | string  | Custom placeholder text if the input is empty; max 100 characters                                 |

###### Text Input Styles

| Name      | Value | Description       |
|-----------|-------|-------------------|
| Short     | 1     | Single-line input |
| Paragraph | 2     | Multi-line input  |


###### Text Input Interaction

```json
{
    "application_id": "845027738276462632",
    "channel": {
        "flags": 0,
        "guild_id": "772904309264089089",
        "id": "772908445358620702",
        "last_message_id": "113817814796417433",
        "name": "general",
        "nsfw": false,
        "parent_id": "1113560261366927532",
        "permissions": "281474976710655",
        "position": 0,
        "rate_limit_per_user": 0,
        "topic": null,
        "type": 0
    },
    "channel_id": "772908445358620702",
    "data": {
        "custom_id": "cool_modal",
        "components": [
            {
                "id": 1,
                "type": 1,
                "components": [
                    {
                        "id": 2,
                        "custom_id": "name",
                        "type": 4,
                        "value": "John"
                    }
                ]
            }
        ]
    },
    "guild_id": "772904309264089089",
    "id": "847587388497854464",
    "member": {
        "avatar": null,
        "deaf": false,
        "is_pending": false,
        "joined_at": "2020-11-02T19:25:47.248000+00:00",
        "mute": false,
        "nick": null,
        "pending": false,
        "permissions": "17179869183",
        "premium_since": null,
        "roles": ["785609923542777878"],
        "user": {
            "avatar": "a_d5efa99b3eeaa7dd43acca82f5692432",
            "global_name": "Mason",
            "discriminator": "0",
            "id": "53908232506183680",
            "public_flags": 131141,
            "username": "Mason"
        }
    },
    "token": "UNIQUE_TOKEN",
    "type": 5,
    "version": 1
}
```

# V2 Components

These are new components, requiring the [message flag](#DOCS_RESOURCES_MESSAGE/message-object-message-flags) `1 << 15`.

You can have a maximum of `10` top-level components per message.
The total number of nested components (including top level) cannot exceed `30`.

The upper limit of text characters is `4000` in total.

Limitations:
- The ability to set the `content`, `embeds`, `sticker_ids`, and `poll` field will be disabled
- No support for audio files
- No simple text preview for files
- No embeds for urls

## Sections

Section components allow you to define up to 3 text display components and add either a thumbnail or button to the right side.

![A section component](message-components-v2-section.png)

### Section Object

###### Section Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 9,
            "components": [
                {
                    "id": 2,
                    "type": 10,
                    "content": "Hey there"
                },
                {
                    "id": 3,
                    "type": 10,
                    "content": "I'm a text in a section"
                }
            ],
            "accessory": {
                "id": 4,
                "type": 11,
                "media": {
                    "url": "https://i.imgur.com/SpCbHBI.jpeg"
                },
                "description": "Condescending Cat is not happy with me",
                "spoiler": false
            }
        }
    ]
}
```

###### Section Structure

| Field      | Type                                                                                                              | Description                                                                                                                                                         |
|------------|-------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id?        | integer                                                                                                           | Unique identifier for the component; auto populated through increment if not provided.                                                                              |
| type       | integer                                                                                                           | `9` for a section component                                                                                                                                         |
| components | array of [text display objects](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/text-display-object-text-display-structure) | Array of text display components; max of 3.                                                                                                                         |
| accessory  | component                                                                                                         | An accessory component, can be [Thumbnail](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/thumbnail-object) or [Button](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/button-object) |

## Text Displays

A text display component allows you to send text.

![A text display component](message-components-v2-text-display.png)

### Text Display Object

###### Text Display Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 10,
            "content": "I'm a text"
        }
    ]
}
```

###### Text Display Structure

| Field   | Type    | Description                               |
|---------|---------|-------------------------------------------|
| type    | integer | `10` for a text display component         |
| content | string  | The content of the text display component |

## Thumbnails

A thumbnail component can be used as an accessory for a section component.

### Thumbnail Object

###### Thumbnail Structure

| Field        | Type                                                                                           | Description                                                                            |
|--------------|------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| id?          | integer                                                                                        | Unique identifier for the component; auto populated through increment if not provided. |
| type         | integer                                                                                        | `11` for a thumbnail component                                                         |
| media        | [unfurled media item object](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/unfurled-media-item-object) | Media item for the thumbnail                                                           |
| description? | ?string                                                                                        | Description for the thumbnail (max 256 characters). Defaults to `null`                 |
| spoiler?     | boolean                                                                                        | Whether the thumbnail is a spoiler. Defaults to `false`                                |

## Media Galleries

Media gallery components allow you to group images, videos or gifs into a gallery grid.

![A media gallery component](message-components-v2-media-gallery.png)

### Media Gallery Object

###### Media Gallery Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 12,
            "items": [
                {
                    "media": {
                        "url": "https://i.imgur.com/JOKsNeT.jpeg"
                    },
                    "description": "Cat I'm a kitty cat",
                    "spoiler": false
                },
                {
                    "media": {
                        "url": "https://i.imgur.com/ujAO1Dl.mp4"
                    },
                    "description": "Cat wearing an anglerfish costume",
                    "spoiler": false
                }
            ]
        }
    ]
}
```

###### Media Gallery Structure

| Field | Type                                                                                                                           | Description                                                                            |
|-------|--------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| id?   | integer                                                                                                                        | Unique identifier for the component; auto populated through increment if not provided. |
| type  | integer                                                                                                                        | `12` for a media gallery component                                                     |
| items | array of [media gallery item objects](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/media-gallery-object-media-gallery-item-structure) | Array of media gallery items; max of 10.                                               |

###### Media Gallery Item Structure

| Field        | Type                                                                                           | Description                                                               |
|--------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| media        | [unfurled media item object](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/unfurled-media-item-object) | Media item for the gallery                                                |
| description? | ?string                                                                                        | Description for the gallery item (max 256 characters). Defaults to `null` |
| spoiler?     | boolean                                                                                        | Whether the gallery item is a spoiler. Defaults to `false`                |

## File Components

File components allow you to send a file. You can also spoiler it.

Note: There is no "preview" support for simple text files, nor audio file support.

![A file component](message-components-v2-file.png)

### File Object

###### File Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 13,
            "file": {
                "url": "attachment://file.txt"
            }
        }
    ],
    "attachments": [
        {
            "id": 0,
            "filename": "file.txt"
        }
    ]
}
```

###### File Structure

| Field    | Type                                                                                           | Description                                                                            |
|----------|------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| id?      | integer                                                                                        | Unique identifier for the component; auto populated through increment if not provided. |
| type     | integer                                                                                        | `13` for a file component                                                              |
| file     | [unfurled media item object](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/unfurled-media-item-object) | The file to be displayed, supports only `attachment://<filename>` references           |
| spoiler? | boolean                                                                                        | Whether the file is a spoiler. Defaults to `false`                                     |

## Separators

Separator components allow you to divide components with a divider. You can make the divider big or small, and make it invisible if needed.

![A separator component](message-components-v2-separator.png)

### Separator Object

###### Separator Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 10,
            "content": "I'm a text"
        },
        {
            "id": 2,
            "type": 14,
            "divider": true,
            "spacing": 1
        },
        {
            "id": 3,
            "type": 10,
            "content": "I'm yet another text, but above me is a dividing separator."
        }
    ]
}
```

###### Separator Structure

| Field    | Type                                                                                                    | Description                                                                            |
|----------|---------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|
| id?      | integer                                                                                                 | Unique identifier for the component; auto populated through increment if not provided. |
| type     | integer                                                                                                 | `14` for a separator component                                                         |
| divider? | boolean                                                                                                 | Whether the separator is a divider. Defaults to `true`                                 |
| spacing? | [seperator spacing size](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/separator-object-separator-spacing-size) | Spacing size for the separator. Defaults to `1`                                        |

###### Separator Spacing Size

| Size  | Value | Description        |
|-------|-------|--------------------|
| SMALL | 1     | Small spacing size |
| LARGE | 2     | Large spacing size |

## Containers

Containers are a new way to group components together. You can also specify a accent color (similar to embeds) and spoiler it.

![A container component](message-components-v2-container.png)

### Container Object

###### Container Example

```json
{
    "flags": 32768,
    "components": [
        {
            "id": 1,
            "type": 17,
            "components": [
                {
                    "id": 2,
                    "type": 9,
                    "components": [
                        {
                            "id": 3,
                            "type": 10,
                            "content": "Hey there"
                        },
                        {
                            "id": 4,
                            "type": 10,
                            "content": "I'm a text in a section"
                        }
                    ],
                    "accessory": {
                        "id": 5,
                        "type": 11,
                        "media": {
                            "url": "https://i.imgur.com/SpCbHBI.jpeg"
                        },
                        "description": "Condescending Cat is not happy with me",
                        "spoiler": false
                    }
                },
                {
                    "id": 6,
                    "type": 12,
                    "items": [
                        {
                            "media": {
                                "url": "https://i.imgur.com/JOKsNeT.jpeg"
                            },
                            "description": "Cat I'm a kitty cat",
                            "spoiler": false
                        },
                        {
                            "media": {
                                "url": "https://i.imgur.com/ujAO1Dl.mp4"
                            },
                            "description": "Cat wearing an anglerfish costume",
                            "spoiler": false
                        }
                    ]
                },
                {
                    "id": 7,
                    "type": 13,
                    "file": {
                        "url": "attachment://file.txt"
                    }
                },
                {
                    "id": 8,
                    "type": 10,
                    "content": "I'm a text"
                },
                {
                    "id": 9,
                    "type": 14,
                    "divider": true,
                    "spacing": 1
                },
                {
                    "id": 10,
                    "type": 10,
                    "content": "I'm yet another text, but above me is a dividing separator."
                }
            ]
        }
    ],
    "attachments": [
        {
            "id": 0,
            "filename": "file.txt"
        }
    ]
}
```

###### Container Structure

| Field         | Type                                                                                 | Description                                                                                                                            |
|---------------|--------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| id?           | integer                                                                              | Unique identifier for the component; auto populated through increment if not provided.                                                 |
| type          | integer                                                                              | `17` for a container                                                                                                                   |
| accent_color? | ?integer                                                                             | Color code for the container. Defaults to `null`                                                                                       |
| spoiler?      | boolean                                                                              | Whether the container is a spoiler. Defaults to `false`                                                                                |
| components    | array of [component objects](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object) | Can be of [type](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/component-object-component-types) `1`, `9`, `10`, `12`, `13` or `14`; max of 10 |


## Unfurled Media Items

Unfurled media items are the base for V2 components. It allows you to specify an arbitrary url or `attachment://<filename>` reference.

Upon resolving by discord, it returns the full object, including metadata about height, width and content type.

### Unfurled Media Item Object

###### Unfurled Media Item Example

```json
{
    "url": "https://i.imgur.com/SpCbHBI.jpeg"
}
```

###### Unfurled Media Item Structure

| Field | Type   | Description                                                      |
|-------|--------|------------------------------------------------------------------|
| url   | string | Supports arbitrary URLs and `attachment://<filename>` references |

###### Unfurled Media Item Loading State
| Value | State            |
|-------|------------------|
| 0     | Unknown          |
| 1     | Loading          |
| 2     | Loaded Success   |
| 3     | Loaded Not Found |

###### Resolved Unfurled Media Item Example

```json
{
    "url": "https://i.imgur.com/SpCbHBI.jpeg",
    "proxy_url": "https://images-ext-1.discordapp.net/external/JnxJ6nc07YuYZoa1zhTq2JW6oHVNJh4fDcTKElOG1F8/https/i.imgur.com/SpCbHBI.jpeg",
    "width": 640,
    "height": 640,
    "placeholder": "GSkKFwQ7d3dgiXiHeKZXWJd2eL+Y94wK",
    "placeholder_version": 1,
    "content_type": "image/jpeg",
    "loading_state": 2,
    "flags": 0
}
```

###### Resolved Unfurled Media Item Structure

// TEMPORARY NOTE: I'm unsure if the nullability / optionality of these fields is correct. Waiting for final confirmation.
// loading_state might be never null, but who knows.

| Field         | Type     | Description                                                                                                         |
|---------------|----------|---------------------------------------------------------------------------------------------------------------------|
| url           | string   | source url of media item (only supports http(s) and attachments)                                                    |
| proxy_url     | ?string  | a proxied url of the media item                                                                                     |
| height        | ?integer | height of media item                                                                                                |
| width         | ?integer | width of media item                                                                                                 |
| content_type  | ?string  | the media item's [media type](https://en.wikipedia.org/wiki/Media_type)                                             |
| loading_state | ?integer | [loading state](#DOCS_INTERACTIONS_MESSAGE_COMPONENTS/unfurled-media-item-object-unfurled-media-item-loading-state) |