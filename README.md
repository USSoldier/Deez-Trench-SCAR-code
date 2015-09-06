# Deez-Trench-SCAR-code
Deez Trench SCAR code
function OnInit()
	sg_temp = SGroup_CreateIfNotFound("sg_temp")
	sg_testpos = SGroup_CreateIfNotFound("sg_testpos")
	eg_temp_check = EGroup_CreateIfNotFound("eg_temp_check")
	eg_allp1 = EGroup_CreateIfNotFound("eg_allp1")
	eg_allp2 = EGroup_CreateIfNotFound("eg_allp2")
	eg_allp3 = EGroup_CreateIfNotFound("eg_allp3")
	eg_allp4 = EGroup_CreateIfNotFound("eg_allp4")
	eg_allp5 = EGroup_CreateIfNotFound("eg_allp5")
	eg_allp6 = EGroup_CreateIfNotFound("eg_allp6")
	eg_allp7 = EGroup_CreateIfNotFound("eg_allp7")
	eg_allp8 = EGroup_CreateIfNotFound("eg_allp8")
	trench_wall = BP_GetEntityBlueprint("ebps/races/allies_commonwealth/buildings/trench_wall_main.lua")
	trench_wall_spawn = BP_GetEntityBlueprint("ebps/races/allies_commonwealth/buildings/trench_wall_secondary.lua")
	trench_wall_rw = BP_GetEntityBlueprint("ebps/races/axis/buildings/rw_trenchwall_main.lua")
	trench_wall_spawn_rw = BP_GetEntityBlueprint("ebps/races/axis/buildings/rw_trenchwall_secondary.lua")
	
	_Players = {player1, player2, player3, player4, player5, player6, player7, player8}
	_EGroups = {eg_allp1, eg_allp2, eg_allp3, eg_allp4, eg_allp5, eg_allp6, eg_allp7, eg_allp8}
	
	for a = 1, World_GetPlayerCount() do
		_Players[a] = World_GetPlayerAt(a)
	end	
end

Scar_AddInit(OnInit)

function Trench_PieceCompleted()
	for i = 1, World_GetPlayerCount() do
		Player_GetAll(_Players[i], sg_temp, _EGroups[i])
		EGroup_Filter(_EGroups[i], trench_wall, FILTER_KEEP)
		mirror_wall = trench_wall_spawn
		if EGroup_IsEmpty(_EGroups[i]) then
			Player_GetAll(_Players[i], sg_temp, _EGroups[i])
			mirror_wall = trench_wall_spawn_rw
			EGroup_Filter(_EGroups[i], trench_wall_rw, FILTER_KEEP)
		end
		if not EGroup_IsEmpty(_EGroups[i]) then
			EGroup_ForEach(_EGroups[i], DO_Duplicate_wall)
		end
	end
	print("Duplicate function called!")
end

function DO_Duplicate_wall(egroupid, itemindex, entityID)
	EGroup_Clear(eg_temp_check)	
	spawnpos = Entity_GetOffsetPosition(entityID, 0, 4)			
	facing = Entity_GetOffsetPosition(entityID, 0, -4)
	World_GetEntitiesNearPoint(Entity_GetPlayerOwner(entityID), eg_temp_check, spawnpos, 1, OT_Player)
	EGroup_Filter(eg_temp_check, trench_wall_spawn, FILTER_KEEP)
	
	if  EGroup_IsEmpty(eg_temp_check) then
		if Entity_GetBuildingProgress(entityID) == 1 then	
			Util_CreateEntities(Entity_GetPlayerOwner(entityID), eg_temp, mirror_wall, spawnpos, 1, facing)
			print("spawned!")
		end		
	end
end
