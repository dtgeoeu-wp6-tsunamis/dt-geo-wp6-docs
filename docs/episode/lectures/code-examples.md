# Working with code examples 

## Example with lines numbers 


```python linenums="1" title="turbine_model.py"
from typing import List

from pydantic import BaseModel, Field


class SoilLayer(BaseModel):
    depth: float = Field(description="Depth from seabed to soil later")
    number_of_elements: int = Field(
        description="Number of elements of this material at this depth"
    )

class TurbineModel(BaseModel):
    soil_layers: List[SoilLayer]
    load_step_num: int = Field(
         default=20, ge=0, description="Number of load steps in cycle"
    )    
    # ... etc. for validation 
```


## Example with highlighted lines 

```python linenums="1" title="turbine_model.py" hl_lines="17 19"
from pydantic import BaseModel, Field


class SoilLayer(BaseModel):
    depth: float = Field(description="Depth from seabed to soil later")
    number_of_elements: int = Field(
        description="Number of elements of this material at this depth"
    )

class TurbineModel(BaseModel):
    soil_layers: list[SoilLayer]
    load_step_num: int = Field(
         default=20, ge=0, description="Number of load steps in cycle"
    )    
    # ... etc. for validation 

soil_layers = [{"depth": 0, "number_of_elements": 2},{"depth":2, "number_of_elements": 3}]

simulation_steps = 20

turbine_model = TurbineModel(
        soil_layers=soil_layers,
        load_step_num = simulation_steps
    )

print(f"My turbine model: {turbine_model}")
```