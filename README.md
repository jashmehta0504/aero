graph TD
    %% Styling definitions
    classDef input fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef python fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef solver fill:#ffebee,stroke:#b71c1c,stroke-width:2px;
    classDef output fill:#e8f5e9,stroke:#1b5e20,stroke-width:2px;
    classDef algo fill:#fff3e0,stroke:#e65100,stroke-width:2px;

    %% --- PHASE 1: USER INPUT & PRE-PROCESSING ---
    subgraph "Phase 1: Controller & Translation Layer"
        User[User Inputs:<br>1. Seed Airfoil (.dat)<br>2. Reynolds Number<br>3. Target Angle of Attack (AoA)]:::input
        
        Trans[Smart Translation Engine<br>(Python Middleware)]:::python
        XfoilCheck[Pre-Analysis: XFOIL Check]:::python
        Math[Math Conversion:<br>AoA → Lift Coefficient (Cl)]:::python
        
        Config[Generate Config File:<br>xopt_input.xo2]:::python
    end

    %% --- PHASE 2: OPTIMIZER CORE ---
    subgraph "Phase 2: Solver Core (The Black Box)"
        Init[Initialize Population:<br>30 Random Particles]:::solver
        
        Loop{Optimization Loop<br>(Iterate 30-50 times)}:::solver
        
        GeoCheck[Geometric Constraint Check:<br>Is Thickness > 12%?]:::solver
        AeroCalc[Aerodynamic Evaluation:<br>Run XFOIL at fixed Cl]:::solver
        
        Fitness[Calculate Fitness Score:<br>Min Drag or Max L/D]:::solver
    end

    %% --- PHASE 3: PSO ALGORITHM ---
    subgraph "Phase 3: Particle Swarm Logic"
        Update[Update Particle Velocity]:::algo
        
        Vec1[Inertia Vector:<br>Keep current momentum]:::algo
        Vec2[Cognitive Vector:<br>Move to Personal Best]:::algo
        Vec3[Social Vector:<br>Move to Global Best]:::algo
        
        NewShape[Generate New Shapes:<br>Modify Camber & Thickness]:::algo
    end

    %% --- PHASE 4: OUTPUT ---
    subgraph "Phase 4: Post-Processing"
        Final[Global Best Found]:::output
        Write[Write File:<br>optimized_foil.dat]:::output
        Verify[Verification:<br>Python runs Final Polar]:::output
        Graph[GUI Output:<br>Plot Comparison Graphs]:::output
    end

    %% CONNECTIONS
    User --> Trans
    Trans --> XfoilCheck
    XfoilCheck -- "Cl Found" --> Math
    Math --> Config
    Config --> Init
    Init --> Loop
    
    Loop --> GeoCheck
    GeoCheck -- "Fail" --> Update
    GeoCheck -- "Pass" --> AeroCalc
    AeroCalc --> Fitness
    Fitness --> Update
    
    Update --> Vec1
    Update --> Vec2
    Update --> Vec3
    Vec1 & Vec2 & Vec3 --> NewShape
    NewShape --> Loop
    
    Loop -- "Convergence Reached" --> Final
    Final --> Write
    Write --> Verify
    Verify --> Graph
