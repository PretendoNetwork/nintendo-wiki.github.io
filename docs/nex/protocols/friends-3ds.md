---
layout: post
toc: true
title: Friends 3DS (101)
---

Official name: `NintendoFriendPresenceProtocol`

## Methods

| Method ID | Method Name                                                           |
| --------- | --------------------------------------------------------------------- |
| 1         | [UpdateProfile](#1-updateprofile)                                     |
| 2         | [UpdateMii](#2-updatemii)                                             |
| 3         | [UpdateMiiList](#3-updatemiilist)                                     |
| 4         | [UpdatePlayedGames](#4-updateplayedgames)                             |
| 5         | [UpdatePreference](#5-updatepreference)                               |
| 6         | [GetFriendMii](#6-getfriendmii)                                       |
| 7         | [GetFriendMiiList](#7-getfriendmiilist)                               |
| 8         | [IsActiveGame](#8-isactivegame)                                       |
| 9         | [GetPrincipalIDByLocalFriendCode](#9-getprincipalidbylocalfriendcode) |
| 10        | [GetFriendRelationships](#10-getfriendrelationships)                  |
| 11        | [AddFriendByPrincipalID](#11-addfriendbyprincipalid)                  |
| 12        | [AddFriendBylstPrincipalID](#12-addfrendbylstprincipalid)             |
| 13        | [RemoveFriendByLocalFriendCode](#13-removefriendbylocalfriendcode)    |
| 14        | [RemoveFriendByPrincipalID](#14-removefriendbyprincipalid)            |
| 15        | [GetAllFriends](#15-getallfriends)                                    |
| 16        | [UpdateBlackList](#16-updateblacklist)                                |
| 17        | [SyncFriend](#17-syncfriend)                                          |
| 18        | [UpdatePresence](#18-updatepresence)                                  |
| 19        | [UpdateFavoriteGameKey](#19-updatefavoritegamekey)                    |
| 20        | [UpdateComment](#20-updatecomment)                                    |
| 21        | [UpdatePicture](#21-updatepicture)                                    |
| 22        | [GetFriendPresence](#22-getfriendpresence)                            |
| 23        | [GetFriendComment](#23-getfriendcomment)                              |
| 24        | [GetFriendPicture](#24-getfriendpicture)                              |
| 25        | [GetFriendPersistentInfo](#25-getfriendpersistentinfo)                |
| 26        | [SendInvitation](#sendinvitation)                                     |

### (1) UpdateProfile
#### Request

| Type                              | Description  |
| --------------------------------- | ------------ |
| [MyProfile](#myprofile-structure) | Profile data |

#### Response
This method does not return anything

### (2) UpdateMii
#### Request

| Type                  | Description |
| --------------------- | ----------- |
| [Mii](#mii-structure) | Mii         |

#### Response
This method does not return anything

### (3) UpdateMiiList
#### Request

| Type                          | Description |
| ----------------------------- | ----------- |
| [MiiList](#miilist-structure) | Mii list    |

#### Response
This method does not return anything.

### (4) UpdatePlayedGames
#### Request

| Type                                              | Description  |
| ------------------------------------------------- | ------------ |
| [List]&lt;[PlayedGame](#playedgame-structure)&gt; | Played games |

#### Response
This method does not return anything.

### (5) UpdatePreference
#### Request

| Type | Description                  |
| ---- | ---------------------------- |
| Bool | Show online presence         |
| Bool | Show currently playing title |
| Bool | Show played titles           |

#### Response
This method does not return anything

### (6) GetFriendMii
#### Request

| Type                                              | Description |
| ------------------------------------------------- | ----------- |
| [List]&lt;[FriendInfo](#friendinfo-structure)&gt; | Friends     |

#### response

| Type                                            | Description |
| ----------------------------------------------- | ----------- |
| [List]&lt;[FriendMii](#friendmii-structure)&gt; | Miis        |

### (7) GetFriendMiiList
#### Request

| Type                                              | Description |
| ------------------------------------------------- | ----------- |
| [List]&lt;[FriendInfo](#friendinfo-structure)&gt; | Friends     |

#### Response

| Type                                                    | Description |
| ------------------------------------------------------- | ----------- |
| [List]&lt;[FriendMiiList](#friendmiilist-structure)&gt; | Mii lists   |

### (8) IsActiveGame
#### Request

| Type                          | Description   |
| ----------------------------- | ------------- |
| [List]&lt;[PID]&gt;           | Principal ids |
| [GameKey](#gamekey-structure) | Game key      |

#### Response

| Type                | Description   |
| ------------------- | ------------- |
| [List]&lt;[PID]&gt; | Principal ids |

### (9) GetPrincipalIDByLocalFriendCode
#### Request

| Type                 | Description        |
| -------------------- | ------------------ |
| Uint64               | Local friend code  |
| [List]&lt;Uint64&gt; | Local friend codes |

#### Response

| Type                                                              | Description          |
| ----------------------------------------------------------------- | -------------------- |
| [List]&lt;[FriendRelationship](#friendrelationship-structure)&gt; | Friend relationships |

### (10) GetFriendRelationships
#### Request

| Type                | Description   |
| ------------------- | ------------- |
| [List]&lt;[PID]&gt; | Principal ids |

#### Response

| Type                                                              | Description          |
| ----------------------------------------------------------------- | -------------------- |
| [List]&lt;[FriendRelationship](#friendrelationship-structure)&gt; | Friend relationships |

### (11) AddFriendByPrincipalID
#### Request

| Type   | Description       |
| ------ | ----------------- |
| Uint64 | Local friend code |
| [PID]  | Principal id      |

#### Response

| Type                                                | Description         |
| --------------------------------------------------- | ------------------- |
| [FriendRelationship](#friendrelationship-structure) | Friend relationship |

### (12) AddFriendBylstPrincipalID
#### Request

| Type                | Description       |
| ------------------- | ----------------- |
| Uint64              | Local friend code |
| [List]&lt;[PID]&gt; | Principal ids     |

#### Response

| Type                                                              | Description          |
| ----------------------------------------------------------------- | -------------------- |
| [List]&lt;[FriendRelationship](#friendrelationship-structure)&gt; | Friend relationships |

### (13) RemoveFriendByLocalFriendCode
#### Request

| Type   | Description       |
| ------ | ----------------- |
| Uint64 | Local friend code |

#### Response
This method does not return anything.

### (14) RemoveFriendByPrincipalID
#### Request

| Type  | Description  |
| ----- | ------------ |
| [PID] | Principal id |

#### Response
This method does not return anything.

### (15) GetAllFriends
#### Request
This method does not take any parameters.

#### Response

| Type                                                              | Description          |
| ----------------------------------------------------------------- | -------------------- |
| [List]&lt;[FriendRelationship](#friendrelationship-structure)&gt; | Friend relationships |

### (16) UpdateBlackList
#### Request

| Type                 | Description |
| -------------------- | ----------- |
| [List]&lt;Uint32&gt; | Unknown     |

#### Response
This method does not return anything.

### (17) SyncFriend
#### Request

| Type                 | Description        |
| -------------------- | ------------------ |
| Uint64               | Local friend code  |
| [List]&lt;[PID]&gt;  | Principal ids      |
| [List]&lt;Uint64&gt; | Local friend codes |

#### Response

| Type                                                              | Description |
| ----------------------------------------------------------------- | ----------- |
| [List]&lt;[FriendRelationship](#friendrelationship-structure)&gt; | Friend list |

### (18) UpdatePresence
#### Request

| Type                                            | Description                  |
| ----------------------------------------------- | ---------------------------- |
| [NintendoPresence](#nintendopresence-structure) | Presence info                |
| Bool                                            | Show currently playing title |

#### Response
This method does not return anything

### (19) UpdateFavoriteGameKey
#### Request

| Type                          | Description |
| ----------------------------- | ----------- |
| [GameKey](#gamekey-structure) | Game key    |

#### Response
This method does not return anything.

### (20) UpdateComment
#### Request

| Type     | Description |
| -------- | ----------- |
| [String] | Comment     |

#### Response
This method does not return anything

### (21) UpdatePicture
#### Request

| Type     | Description |
| -------- | ----------- |
| Uint32   | Unknown     |
| [Buffer] | Picture     |

#### Response
This method does not return anything

### (22) GetFriendPresence
#### Request

| Type                | Description   |
| ------------------- | ------------- |
| [List]&lt;[PID]&gt; | Principal ids |

#### Response

| Type                                                      | Description          |
| --------------------------------------------------------- | -------------------- |
| [List]&lt;[FriendPresence](#friendpresence-structure)&gt; | Friend presence list |

### (23) GetFriendComment
#### Request

| Type                                              | Description |
| ------------------------------------------------- | ----------- |
| [List]&lt;[FriendInfo](#friendinfo-structure)&gt; | Friend info |

#### Response

| Type                                                    | Description |
| ------------------------------------------------------- | ----------- |
| [List]&lt;[FriendComment](#friendcomment-structure)&gt; | Comments    |

### (24) GetFriendPicture
#### Request

| Type                 | Description |
| -------------------- | ----------- |
| [List]&lt;Uint32&gt; | Unknown     |

#### Response

| Type                                                    | Description     |
| ------------------------------------------------------- | --------------- |
| [List]&lt;[FriendPicture](#friendpicture-structure)&gt; | Friend pictures |

### (25) GetFriendPersistentInfo
#### Request

| Type                | Description   |
| ------------------- | ------------- |
| [List]&lt;[PID]&gt; | Principal ids |

#### Response

| Type                                                                  | Description     |
| --------------------------------------------------------------------- | --------------- |
| [List]&lt;[FriendPersistentInfo](#friendpersistentinfo-structure)&gt; | Persistent info |

### (26) SendInvitation
#### Request

| Type                | Description   |
| ------------------- | ------------- |
| [List]&lt;[PID]&gt; | Principal ids |

#### Response
This method does not return anything

## Types
### FriendComment ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type       | Description  |
| ---------- | ------------ |
| [PID]      | Principal id |
| [String]   | Comment      |
| [DateTime] | Modified at  |

### FriendInfo ([Structure])

| Type       | Description  |
| ---------- | ------------ |
| [PID]      | Principal id |
| [DateTime] | Unknown      |

### FriendMii ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                  | Description  |
| --------------------- | ------------ |
| [PID]                 | Principal id |
| [Mii](#mii-structure) | Mii          |
| [DateTime]            | Modified at  |

### FriendMiiList ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                          | Description |
| ----------------------------- | ----------- |
| Uint32                        | Unknown     |
| [MiiList](#miilist-structure) | Mii list    |
| [DateTime]                    | Unknown     |

### FriendPersistentInfo ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                          | Description        |
| ----------------------------- | ------------------ |
| [PID]                         | User id            |
| Uint8                         | Region             |
| Uint8                         | Country            |
| Uint8                         | Area               |
| Uint8                         | Language           |
| Uint8                         | Platform           |
| [GameKey](#gamekey-structure) | Game key           |
| [String]                      | Message            |
| [DateTime]                    | Message updated at |
| [DateTime]                    | Mii modified at    |
| [DateTime]                    | Last online at     |

### FriendPicture ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type       | Description |
| ---------- | ----------- |
| Uint32     | Unknown     |
| [Buffer]   | Data        |
| [DateTime] | Date time   |

### FriendPresence ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                                            | Description       |
| ----------------------------------------------- | ----------------- |
| [PID]                                           | Principal id      |
| [NintendoPresence](#nintendopresence-structure) | Nintendo presence |

### FriendRelationship ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type   | Description       |
| ------ | ----------------- |
| [PID]  | Principal id      |
| Uint64 | Local friend code |
| Uint8  | Relationship type |

Relationship types:

| Type   | Description           |
| ------ | --------------------- |
| 0      | Friendship incomplete |
| 1      | Friendship complete   |
| 2      | Friend does not exist |

### GameKey ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type   | Name          | Description   |
| ------ | ------------- | ------------- |
| Uint64 | m_gameCode    | Title id      |
| Uint16 | m_gameVersion | Title version |

### Mii ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type     | Description    |
| -------- | -------------- |
| [String] | Name           |
| Bool     | Profanity flag |
| Uint8    | Character set  |
| [Buffer] | Mii data       |

Character sets:

| Value | Description     |
|-------|-----------------|
| 0     | JPN + USA + EUR |
| 1     | CHN             |
| 2     | KOR             |
| 3     | TWN             |

### MiiList ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                   | Description   |
| ---------------------- | ------------- |
| [String]               | Unknown       |
| Bool                   | Unknown       |
| Uint8                  | Unknown       |
| [List]&lt;[Buffer]&gt; | Mii data list |

### MyProfile ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type     | Description            |
| -------- | ---------------------- |
| Uint8    | Region                 |
| Uint8    | Country                |
| Uint8    | Area                   |
| Uint8    | Language               |
| Uint8    | Platform               |
| Uint64   | Local friend code seed |
| [String] | Console MAC address    |
| [String] | Console serial number  |

### NintendoPresence ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                          | Name                   |
| ----------------------------- | ---------------------- |
| Uint32                        | m_changedBitFlag       |
| [GameKey](#gamekey-structure) | m_gameKey              |
| [String]                      | m_gameModeDescription  |
| Uint32                        | m_joinAvailabilityFlag |
| Uint8                         | m_matchmakeSystemType  |
| Uint32                        | m_joinGameID           |
| Uint32                        | m_joinGameMode         |
| [PID]                         | m_ownerPrincipalID     |
| Uint32                        | m_joinGroupID          |
| [Buffer]                      | m_applicationArg       |

Change bit flags:

| Flag                          | Description                             |
| ----------------------------- | --------------------------------------- |
| 0x1                           | [Game Key](#gamekey-structure) changed. |
| 0x2                           | Game mode description changed.          |
| 0x4                           | Join availability flag changed.         |
| 0x8                           | Matchmake system type changed.          |
| 0x10                          | Join game ID changed.                   |
| 0x20                          | Join game mode changed.                 |
| 0x40                          | Owner [PID] changed.                    |
| 0x80                          | Join group ID changed.                  |
| 0x100                         | Application argument changed.           |

### PlayedGame ([Structure])
> This structure [inherits](/docs/nex/types#structure-inheritance) from [Data]
{: .prompt-info }

| Type                          | Description |
| ----------------------------- | ----------- |
| [GameKey](#gamekey-structure) | Game key    |
| [DateTime]                    | Date time   |

[Structure]: NEX-Common-Types#structure
[Data]: NEX-Common-Types#data-structure
[String]: NEX-Common-Types#string
[List]: NEX-Common-Types#list
[Buffer]: NEX-Common-Types#buffer
[DateTime]: NEX-Common-Types#datetime
[PID]: NEX-Common-Types#pid
