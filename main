-- Procedural Maze Generation Script  
-- Generates a maze using the recursive backtracking algorithm. 
-- The hight, width, cell size, trap and collectible can be customized
-- Traps that deal damage when stepped on  
-- Collectibles that disappear when touched

local Maze = {}
Maze.__index = Maze

local Cell = {}
Cell.__index = Cell

function Cell.new(x, y)
	local self = setmetatable({}, Cell)
	self.x = x
	self.y = y
	self.visited = false
	self.walls = { top = true, right = true, bottom = true, left = true }
	self.trap = false
	self.collectible = false
	return self
end

function Maze.new(width, height, cellSize)
	local self = setmetatable({}, Maze)
	self.width = width
	self.height = height
	self.cellSize = cellSize
	self.cells = {}
	for x = 1, width do
		self.cells[x] = {}
		for y = 1, height do
			self.cells[x][y] = Cell.new(x, y)
		end
	end
	return self
end

function Maze:getNeighbors(cell)
	local neighbors = {}
	local directions = {
		{dx = 0, dy = -1, wall = "top", opposite = "bottom"},
		{dx = 1, dy = 0, wall = "right", opposite = "left"},
		{dx = 0, dy = 1, wall = "bottom", opposite = "top"},
		{dx = -1, dy = 0, wall = "left", opposite = "right"}
	}
	for _, dir in ipairs(directions) do
		local nx = cell.x + dir.dx
		local ny = cell.y + dir.dy
		if nx >= 1 and nx <= self.width and ny >= 1 and ny <= self.height then
			local neighbor = self.cells[nx][ny]
			if not neighbor.visited then
				table.insert(neighbors, {cell = neighbor, wall = dir.wall, opposite = dir.opposite})
			end
		end
	end
	return neighbors
end

function Maze:generateMaze()
	local stack = {}
	local current = self.cells[1][1]
	current.visited = true

	while true do
		local neighbors = self:getNeighbors(current)
		if #neighbors > 0 then
			local rnd = math.random(1, #neighbors)
			local chosen = neighbors[rnd]
			local nextCell = chosen.cell

			current.walls[chosen.wall] = false
			nextCell.walls[chosen.opposite] = false

			table.insert(stack, current)
			nextCell.visited = true
			current = nextCell
		elseif #stack > 0 then
			current = table.remove(stack)
		else
			break
		end
	end
end

function Maze:placeTrapsAndCollectibles(probTrap, probCollectible)
	for x = 1, self.width do
		for y = 1, self.height do
			local cell = self.cells[x][y]
			if not (x == 1 and y == 1) then
				if math.random() < probTrap then
					cell.trap = true
				elseif math.random() < probCollectible then
					cell.collectible = true
				end
			end
		end
	end
end

function Maze:buildMazeInWorld(parent)
	local wallHeight = 10  
	for x = 1, self.width do
		for y = 1, self.height do
			local cell = self.cells[x][y]
			local baseX = (x - 1) * self.cellSize
			local baseZ = (y - 1) * self.cellSize
			local half = self.cellSize / 2

			local floor = Instance.new("Part")
			floor.Size = Vector3.new(self.cellSize, 1, self.cellSize)
			floor.Anchored = true
			floor.Position = Vector3.new(baseX, 0, baseZ)
			floor.Parent = parent
			floor.Name = "Floor_" .. x .. "_" .. y

			if cell.walls.top then
				local wall = Instance.new("Part")
				wall.Size = Vector3.new(self.cellSize, wallHeight, 1)
				wall.Anchored = true
				wall.Position = Vector3.new(baseX, wallHeight/2, baseZ - half)
				wall.Parent = parent
				wall.Name = "Wall_Top_" .. x .. "_" .. y
			end

			if cell.walls.right then
				local wall = Instance.new("Part")
				wall.Size = Vector3.new(1, wallHeight, self.cellSize)
				wall.Anchored = true
				wall.Position = Vector3.new(baseX + half, wallHeight/2, baseZ)
				wall.Parent = parent
				wall.Name = "Wall_Right_" .. x .. "_" .. y
			end

			if cell.walls.bottom then
				local wall = Instance.new("Part")
				wall.Size = Vector3.new(self.cellSize, wallHeight, 1)
				wall.Anchored = true
				wall.Position = Vector3.new(baseX, wallHeight/2, baseZ + half)
				wall.Parent = parent
				wall.Name = "Wall_Bottom_" .. x .. "_" .. y
			end

			if cell.walls.left then
				local wall = Instance.new("Part")
				wall.Size = Vector3.new(1, wallHeight, self.cellSize)
				wall.Anchored = true
				wall.Position = Vector3.new(baseX - half, wallHeight/2, baseZ)
				wall.Parent = parent
				wall.Name = "Wall_Left_" .. x .. "_" .. y
			end

			if cell.trap then
				local trap = Instance.new("Part")
				trap.Size = Vector3.new(self.cellSize / 2, 1, self.cellSize / 2)
				trap.Anchored = true
				trap.BrickColor = BrickColor.new("Bright red")
				trap.Position = Vector3.new(baseX, 1, baseZ)
				trap.Parent = parent
				trap.Name = "Trap_" .. x .. "_" .. y
				trap.Touched:Connect(function(hit)
					local humanoid = hit.Parent:FindFirstChildOfClass("Humanoid")
					if humanoid then
						print("¡Trampa activada por " .. hit.Parent.Name .. "!")
						humanoid:TakeDamage(10)
					end
				end)
			end

			if cell.collectible then
				local collectible = Instance.new("Part")
				collectible.Size = Vector3.new(self.cellSize / 3, self.cellSize / 3, self.cellSize / 3)
				collectible.Anchored = true
				collectible.BrickColor = BrickColor.new("Bright yellow")
				collectible.Position = Vector3.new(baseX, 2, baseZ)
				collectible.Parent = parent
				collectible.Name = "Collectible_" .. x .. "_" .. y
				collectible.Touched:Connect(function(hit)
					local humanoid = hit.Parent:FindFirstChildOfClass("Humanoid")
					if humanoid then
						print("Coleccionable recogido por " .. hit.Parent.Name .. "!")
						collectible:Destroy()
					end
				end)
			end
		end
	end
end

local width = 10         
local height = 10     
local cellSize = 10        
local probTrap = 0.1       
local probCollectible = 0.2  

local maze = Maze.new(width, height, cellSize)
maze:generateMaze()
maze:placeTrapsAndCollectibles(probTrap, probCollectible)
maze:buildMazeInWorld(workspace)

print("Laberinto generado exitosamente")
