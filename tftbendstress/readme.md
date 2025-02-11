# (c) Silvaco Inc., 2024

# Bowing and Bending of beam

# Required Modules
#  Victory Process
#   -2D Stress

# Example Name
SET name=rvce_tft

##################### PROCESS SIMULATION ################################
GO victoryprocess simflags="-P 4"

INIT material=polyimide meshdepth=2 depth=10 gasheight=15 from="-50" to="50" resolution=1

#Using Method statement to set numerical solver for stress
METHOD STRESS.solver=xms STRESS.ANISO=on

LINE x location=-50 s=2.5
LINE x location=-15  s=0.25
LINE x location=-5  s=0.25
LINE x location=-3.5  s=0.25
LINE x location=0 s=0.5
LINE x location=3.5  s=0.25
LINE x location=5  s=0.25
LINE x location=15 s=0.25
LINE x location=50 s=2.5

LINE z location=-1.3 s=0.025
LINE z location=-1.2 s=0.025
LINE z location=-1.0 s=0.025
LINE z location=-0.8  s=0.025
LINE z location=-0.6 s=0.025
LINE z location=-0.5 s=0.025
LINE z location=-0.3 s=0.025
LINE z location=0 s=0.1
LINE z location=10 s=0.5

#SiO2 buffer
DEPOSIT oxide thick=0.3 max

#aluminum gate
DEPOSIT material=aluminum thick=0.2  max

#pattern gate
ETCH material=aluminum left.to.x=-5 max angle=45
ETCH material=aluminum right.to.x=5 max angle=45

# SiNx
DEPOSIT material=nitride thick=0.3 conformal

# a-IGZO
DEPOSIT material=igzo thick=0.2 conformal

#source/drain
DEPOSIT material=aluminum thick=0.2 conformal

#channel length=7um
ETCH material=aluminum between.x="-3.5, 3.5" max

#active pattern
ETCH material=aluminum left.to.x=-15 max 
ETCH material=aluminum right.to.x=15 max 

ETCH material=igzo left.to.x=-15 max 
ETCH material=igzo right.to.x=15 max 

#Using Conformalstr remeshes the saved structure using conformal refinement and creates a structure that can be visualized
SAVE   name="$"name"_init" conformalstr

# Bending over cylinder
# =====================
# Note : We do not recommend to bend more than 45 deg

# To bend 10 deg
# ----------------------
STRESS cylinder.cx=-50 cylinder.radius=200 cylinder.angle=30\
       transfer.results

#Saving "deformed" structure. Only for Visualization
EXPORT deformed structure="$"name"_deformed_30deg.str" 
SAVE name= "$"name"_30deg_sv" conformalstr

# To bend -2 deg
# ----------------------
LOAD name="$"name"_init"

STRESS cylinder.cx=-50 cylinder.radius=200 cylinder.angle=-2 \
       transfer.results

EXPORT deformed structure="$"name"_deformed_m2deg.str" 
SAVE name=$"name"_m2deg_sv conformalstr

# Visualization of "deformed" structures.
victoryvisual $"name"_deformed_10deg.str\
 $"name"_deformed_m2deg.str

QUIT
