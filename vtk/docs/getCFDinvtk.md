# VTK openfoam reader


##-read time step 
```markdown
TimeValues = reader.GetTimeValues()
CurrentTimePoint = TimeValues.GetNumberOfTuples()-1
MaxTimePoint = CurrentTimePoint
CurrentTimeValue = TimeValues.GetTuple1(CurrentTimePoint)
```

##-config reader
```markdown
CurrentTimeValue = TimeValues.GetTuple1(CurrentTimePoint)
reader.ReadZonesOff()
reader.SetTimeValue(CurrentTimeValue)
reader.ReadZonesOn()
reader.Update()
Block = vtk.vtkUnstructuredGrid.SafeDownCast(reader.GetOutput().GetBlock(0)) 
```
##-from now on we got the block that contains cfd result

###1.	Find given points in volume
```markdown
  pointTree.BuildLocatorFromPoints(points)
  pointTree.FindClosestNPoints(k,[X,Y,Z],result) #which XYZ is the coordinate of given point
  p = result.GetId(0)    #p-> result of point id 
```     
###2.	Based on 1, find cells with give point
```markdown
  for iPt in range(Block.GetNumberOfPoints()): #possible multiple closet pts
              if iPt == p:  
                  neighbor_cellIds = vtk.vtkIdList()
                  Block.GetPointCells(iPt, neighbor_cellIds) # possible multiple closet cell id
                  for i in range(neighbor_cellIds.GetNumberOfIds()):
                      iv = neighbor_cellIds.GetId(i) # closet cell id
                      result= iv                
```
###3.	Get parameter of CFD result. Pressure(P) as an example.
```markdown
  P = Block.GetCellData().GetScalars("P")
```
