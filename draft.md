```lua
local isNewPlayer = ECHostPlayerInfo.Instance():IsNewPlayer()
local stateInfo = ECGeneralMan.Instance():GetGeneralBodyStateInfo(heroId, isNewPlayer)
local showState = stateInfo.hasState

-- name
local state_desc = GeneralCfg.state_cfg[ECGeneralMan.STATE_TYPE.BODY_STATE].desc
stateName = state_desc[stateInfo.type] or ""

-- time
local leftTime = stateInfo.leftTime

```

