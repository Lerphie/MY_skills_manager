using { /UnrealEngine.com/Temporary/UI }
using { /Fortnite.com/Devices }
using { /Verse.org/Simulation }
using { /UnrealEngine.com/Temporary/Diagnostics }
using { /Fortnite.com/UI }
using { /UnrealEngine.com/Temporary/SpatialMath }
using { /Verse.org/Assets }
using { /Verse.org/Colors }

# ==============================================================================
#  КОНФИГУРАЦИЯ ЦЕН
# ==============================================================================
Skill_Price_Config := class<concrete>:
    # Индекс 0 = Цена за 1-ю иконку (уровень 1)
    # Индекс 1 = Цена за 2-ю иконку (уровень 2) и т.д.
    @editable LevelPrices : []SpendableResource = array{}

# ==============================================================================
#  КОНФИГУРАЦИЯ НАГРАД (РЕСУРСЫ ЗА ПОКУПКУ)
# ==============================================================================
Skill_Reward_Config := class<concrete>:
    @editable LevelRewards : []GainableResource = array{}

skills_menu := class:
    # 3 страницы меню
    Page1 : MyWidgets.skills_manager_page1 = MyWidgets.skills_manager_page1{}
    Page2 : MyWidgets.skills_manager_page2 = MyWidgets.skills_manager_page2{}
    Page3 : MyWidgets.skills_manager_page3 = MyWidgets.skills_manager_page3{}

    var CurrentPage : int = 1
    TotalPages : int = 3

    # События
    var MenuCloseEvent : event() = event(){}
    var ExitEvent : event() = event(){}
    var PrevPageEvent : event() = event(){}
    var NextPageEvent : event() = event(){}
    SkillPoint : ?texture = false

    # --- Resources & Configs ---
    Owner : ?player = false
    ResourceManager : ?Resource_Manager = false
    PersistencePrefix : string = "SKMENU"
    TargetCurrencyName : string = ""

    # Звуки
    PurchaseSound : audio_player_device = audio_player_device{}
    FailedSound : audio_player_device = audio_player_device{}

    # --- Настройки цен ---
    P1_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    P1_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    P1_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    P1_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    P2_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    P2_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    P2_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    P2_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    P3_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    P3_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    P3_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    P3_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    # --- Настройки наград ---
    P1_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P1_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P1_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P1_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    P2_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P2_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P2_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P2_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    P3_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P3_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P3_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    P3_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    # --- Enable/Disable hit1-hit4 per page ---
    P1_EnableHit1 : logic = true
    P1_EnableHit2 : logic = true
    P1_EnableHit3 : logic = true
    P1_EnableHit4 : logic = true

    P2_EnableHit1 : logic = true
    P2_EnableHit2 : logic = true
    P2_EnableHit3 : logic = true
    P2_EnableHit4 : logic = true

    P3_EnableHit1 : logic = true
    P3_EnableHit2 : logic = true
    P3_EnableHit3 : logic = true
    P3_EnableHit4 : logic = true

    IsHitEnabled(PageIndex:int, SkillIndex:int):logic =
        if(PageIndex = 1):
            if(SkillIndex = 1):
                P1_EnableHit1
            else if(SkillIndex = 2):
                P1_EnableHit2
            else if(SkillIndex = 3):
                P1_EnableHit3
            else:
                P1_EnableHit4
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                P2_EnableHit1
            else if(SkillIndex = 2):
                P2_EnableHit2
            else if(SkillIndex = 3):
                P2_EnableHit3
            else:
                P2_EnableHit4
        else:
            if(SkillIndex = 1):
                P3_EnableHit1
            else if(SkillIndex = 2):
                P3_EnableHit2
            else if(SkillIndex = 3):
                P3_EnableHit3
            else:
                P3_EnableHit4

    # UI контейнеры
    # Page 1
    SkillPointsBox_P1_S1 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P1_S2 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P1_S3 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P1_S4 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    
    # Text Blocks for Page 1 (Counters)
    TB_P1_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P1_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P1_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P1_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Text Blocks for Page 1 (COSTS)
    TB_Cost_P1_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P1_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P1_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P1_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Page 2
    SkillPointsBox_P2_S1 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P2_S2 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P2_S3 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P2_S4 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    
    # Text Blocks for Page 2 (Counters)
    TB_P2_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P2_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P2_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P2_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Text Blocks for Page 2 (COSTS)
    TB_Cost_P2_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P2_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P2_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P2_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Page 3
    SkillPointsBox_P3_S1 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P3_S2 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P3_S3 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }
    SkillPointsBox_P3_S4 : stack_box = stack_box{ Orientation := orientation.Horizontal, Slots := array{} }

    # Text Blocks for Page 3 (Counters)
    TB_P3_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P3_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P3_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_P3_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Text Blocks for Page 3 (COSTS)
    TB_Cost_P3_S1 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P3_S2 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P3_S3 : text_block = text_block{DefaultTextColor := NamedColors.White}
    TB_Cost_P3_S4 : text_block = text_block{DefaultTextColor := NamedColors.White}

    # PLAYER CURRENCY DISPLAY
    TB_PlayerCurrency : text_block = text_block{DefaultTextColor := NamedColors.White}

    # Счетчики
    var SkillPointsCount_P1_S1 : int = 0
    var SkillPointsCount_P1_S2 : int = 0
    var SkillPointsCount_P1_S3 : int = 0
    var SkillPointsCount_P1_S4 : int = 0

    var SkillPointsCount_P2_S1 : int = 0
    var SkillPointsCount_P2_S2 : int = 0
    var SkillPointsCount_P2_S3 : int = 0
    var SkillPointsCount_P2_S4 : int = 0

    var SkillPointsCount_P3_S1 : int = 0
    var SkillPointsCount_P3_S2 : int = 0
    var SkillPointsCount_P3_S3 : int = 0
    var SkillPointsCount_P3_S4 : int = 0

    # Hover variables
    var P1_HoverSkill1 : float = 0.5
    var P1_HoverSkill2 : float = 0.5
    var P1_HoverSkill3 : float = 0.5
    var P1_HoverSkill4 : float = 0.5
    var P1_HoverExit : float = 0.5
    var P1_HoverBack : float = 0.5
    var P1_HoverNext : float = 0.5

    var P2_HoverSkill1 : float = 0.5
    var P2_HoverSkill2 : float = 0.5
    var P2_HoverSkill3 : float = 0.5
    var P2_HoverSkill4 : float = 0.5
    var P2_HoverExit : float = 0.5
    var P2_HoverBack : float = 0.5
    var P2_HoverNext : float = 0.5

    var P3_HoverSkill1 : float = 0.5
    var P3_HoverSkill2 : float = 0.5
    var P3_HoverSkill3 : float = 0.5
    var P3_HoverSkill4 : float = 0.5
    var P3_HoverExit : float = 0.5
    var P3_HoverBack : float = 0.5
    var P3_HoverNext : float = 0.5

    var P1_ResetNextOnReturn : logic = false
    var P2_ResetBackOnReturn : logic = false
    var P2_ResetNextOnReturn : logic = false
    var P3_ResetBackOnReturn : logic = false

    RememberNavHoverOnLeave(PageIndex:int):void =
        if(PageIndex = 1):
            if(P1_HoverNext = 1.0):
                set P1_ResetNextOnReturn = true
            else:
                set P1_ResetNextOnReturn = false
        else if(PageIndex = 2):
            if(P2_HoverBack = 1.0):
                set P2_ResetBackOnReturn = true
            else:
                set P2_ResetBackOnReturn = false
            if(P2_HoverNext = 1.0):
                set P2_ResetNextOnReturn = true
            else:
                set P2_ResetNextOnReturn = false
        else:
            if(P3_HoverBack = 1.0):
                set P3_ResetBackOnReturn = true
            else:
                set P3_ResetBackOnReturn = false

    IconPadLeft : float = -24.0
    IconSize : float = 63.0
    PagingIconSize : float = 231.0
    ClickPulseTime : float = 0.2

    GetCurrentVisualWidget():widget =
        if(CurrentPage = 1):
            Page1
        else if(CurrentPage = 2):
            Page2
        else:
            Page3

    MakeHitboxWidget():widget =
        MyWidgets.skills_hitbox_focusable{}

    # --- Buttons ---
    MakeHitButton1():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleSkillButton(1, B)}
        B
    MakeHitButton2():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleSkillButton(2, B)}
        B
    MakeHitButton3():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleSkillButton(3, B)}
        B
    MakeHitButton4():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleSkillButton(4, B)}
        B
    MakeHitExitButton():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleExitButton(B)}
        B
    MakeHitBackButton():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleBackButton(B)}
        B
    MakeHitNextButton():button =
        B := button:
            Slot := button_slot:
                HorizontalAlignment := horizontal_alignment.Fill
                VerticalAlignment := vertical_alignment.Fill
                Widget := MakeHitboxWidget()
        spawn{HandleNextButton(B)}
        B

    # --- Hover Logic ---
    ClearAllHoversExcept(TargetId:int):void =
        if(CurrentPage = 1):
            if(TargetId <> 1):
                if(P1_HoverSkill1 = 1.0):
                    set P1_HoverSkill1 = 0.0
                    set Page1.IsHovering1 = 0.0
            if(TargetId <> 2):
                if(P1_HoverSkill2 = 1.0):
                    set P1_HoverSkill2 = 0.0
                    set Page1.IsHovering2 = 0.0
            if(TargetId <> 3):
                if(P1_HoverSkill3 = 1.0):
                    set P1_HoverSkill3 = 0.0
                    set Page1.IsHovering3 = 0.0
            if(TargetId <> 4):
                if(P1_HoverSkill4 = 1.0):
                    set P1_HoverSkill4 = 0.0
                    set Page1.IsHovering4 = 0.0
            if(TargetId <> 5):
                if(P1_HoverExit = 1.0):
                    set P1_HoverExit = 0.0
                    set Page1.IsHoveringExit = 0.0
            if(TargetId <> 6):
                if(P1_HoverBack = 1.0):
                    set P1_HoverBack = 0.0
                    set Page1.IsHoveringBack = 0.0
            if(TargetId <> 7):
                if(P1_HoverNext = 1.0):
                    set P1_HoverNext = 0.0
                    set Page1.IsHoveringNext = 0.0
        else if(CurrentPage = 2):
            if(TargetId <> 1):
                if(P2_HoverSkill1 = 1.0):
                    set P2_HoverSkill1 = 0.0
                    set Page2.IsHovering1 = 0.0
            if(TargetId <> 2):
                if(P2_HoverSkill2 = 1.0):
                    set P2_HoverSkill2 = 0.0
                    set Page2.IsHovering2 = 0.0
            if(TargetId <> 3):
                if(P2_HoverSkill3 = 1.0):
                    set P2_HoverSkill3 = 0.0
                    set Page2.IsHovering3 = 0.0
            if(TargetId <> 4):
                if(P2_HoverSkill4 = 1.0):
                    set P2_HoverSkill4 = 0.0
                    set Page2.IsHovering4 = 0.0
            if(TargetId <> 5):
                if(P2_HoverExit = 1.0):
                    set P2_HoverExit = 0.0
                    set Page2.IsHoveringExit = 0.0
            if(TargetId <> 6):
                if(P2_HoverBack = 1.0):
                    set P2_HoverBack = 0.0
                    set Page2.IsHoveringBack = 0.0
            if(TargetId <> 7):
                if(P2_HoverNext = 1.0):
                    set P2_HoverNext = 0.0
                    set Page2.IsHoveringNext = 0.0
        else:
            if(TargetId <> 1):
                if(P3_HoverSkill1 = 1.0):
                    set P3_HoverSkill1 = 0.0
                    set Page3.IsHovering1 = 0.0
            if(TargetId <> 2):
                if(P3_HoverSkill2 = 1.0):
                    set P3_HoverSkill2 = 0.0
                    set Page3.IsHovering2 = 0.0
            if(TargetId <> 3):
                if(P3_HoverSkill3 = 1.0):
                    set P3_HoverSkill3 = 0.0
                    set Page3.IsHovering3 = 0.0
            if(TargetId <> 4):
                if(P3_HoverSkill4 = 1.0):
                    set P3_HoverSkill4 = 0.0
                    set Page3.IsHovering4 = 0.0
            if(TargetId <> 5):
                if(P3_HoverExit = 1.0):
                    set P3_HoverExit = 0.0
                    set Page3.IsHoveringExit = 0.0
            if(TargetId <> 6):
                if(P3_HoverBack = 1.0):
                    set P3_HoverBack = 0.0
                    set Page3.IsHoveringBack = 0.0
            set P3_HoverNext = 0.5
            set Page3.IsHoveringNext = 0.5

    var HoverTransitionLocked : logic = false
    HoverTransitionLockTime : float = 0.08
    StartHoverTransitionLock():void =
        set HoverTransitionLocked = true
        spawn{EndHoverTransitionLockAfterDelay(HoverTransitionLockTime)}
    EndHoverTransitionLockAfterDelay(Delay:float)<suspends>:void =
        Sleep(Delay)
        set HoverTransitionLocked = false
    var HoverActiveId : int = 0
    var HoverPendingId : int = 0
    var HoverPendingGen : int = 0
    var HoverOffPendingGen : int = 0
    HoverCoalesceTime : float = 0.03
    ResetHoverArbiter():void =
        set HoverPendingId = 0
        set HoverPendingGen += 1
        set HoverOffPendingGen += 1
        set HoverActiveId = 0
    ApplyHoverById(TargetId:int, Value:float):void =
        if(TargetId = 1):
            SetHover(1, Value)
        else if(TargetId = 2):
            SetHover(2, Value)
        else if(TargetId = 3):
            SetHover(3, Value)
        else if(TargetId = 4):
            SetHover(4, Value)
        else if(TargetId = 5):
            SetHoverExit(Value)
        else if(TargetId = 6):
            SetHoverBack(Value)
        else if(TargetId = 7):
            SetHoverNext(Value)
    RequestHoverOn(TargetId:int):void =
        set HoverPendingId = TargetId
        set HoverPendingGen += 1
        LocalGen := HoverPendingGen
        spawn{ApplyPendingHover(LocalGen)}
    ApplyPendingHover(LocalGen:int)<suspends>:void =
        Sleep(HoverCoalesceTime)
        if(LocalGen = HoverPendingGen):
            set HoverActiveId = HoverPendingId
            ApplyHoverById(HoverPendingId, 1.0)
    RequestHoverOff(TargetId:int):void =
        set HoverOffPendingGen += 1
        LocalGen := HoverOffPendingGen
        spawn{ApplyPendingUnhover(TargetId, LocalGen)}
    ApplyPendingUnhover(TargetId:int, LocalGen:int)<suspends>:void =
        Sleep(HoverCoalesceTime)
        if(LocalGen = HoverOffPendingGen):
            if(HoverActiveId = TargetId):
                ApplyHoverById(TargetId, 0.0)
                set HoverActiveId = 0
    ForceHover(TargetId:int):void =
        ResetHoverArbiter()
        set HoverActiveId = TargetId
        ApplyHoverById(TargetId, 1.0)
    SetHover(Index:int, Value:float):void =
        if(Value = 1.0):
            ClearAllHoversExcept(Index)
        if(CurrentPage = 1):
            if(Index = 1):
                set P1_HoverSkill1 = Value
                set Page1.IsHovering1 = Value
            else if(Index = 2):
                set P1_HoverSkill2 = Value
                set Page1.IsHovering2 = Value
            else if(Index = 3):
                set P1_HoverSkill3 = Value
                set Page1.IsHovering3 = Value
            else:
                set P1_HoverSkill4 = Value
                set Page1.IsHovering4 = Value
        else if(CurrentPage = 2):
            if(Index = 1):
                set P2_HoverSkill1 = Value
                set Page2.IsHovering1 = Value
            else if(Index = 2):
                set P2_HoverSkill2 = Value
                set Page2.IsHovering2 = Value
            else if(Index = 3):
                set P2_HoverSkill3 = Value
                set Page2.IsHovering3 = Value
            else:
                set P2_HoverSkill4 = Value
                set Page2.IsHovering4 = Value
        else:
            if(Index = 1):
                set P3_HoverSkill1 = Value
                set Page3.IsHovering1 = Value
            else if(Index = 2):
                set P3_HoverSkill2 = Value
                set Page3.IsHovering2 = Value
            else if(Index = 3):
                set P3_HoverSkill3 = Value
                set Page3.IsHovering3 = Value
            else:
                set P3_HoverSkill4 = Value
                set Page3.IsHovering4 = Value
    PulseClick(Index:int)<suspends>:void =
        if(CurrentPage = 1):
            if(Index = 1):
                set Page1.PlayClickAnim1 = 1.0
                Sleep(ClickPulseTime)
                set Page1.PlayClickAnim1 = 0.5
            else if(Index = 2):
                set Page1.PlayClickAnim2 = 1.0
                Sleep(ClickPulseTime)
                set Page1.PlayClickAnim2 = 0.5
            else if(Index = 3):
                set Page1.PlayClickAnim3 = 1.0
                Sleep(ClickPulseTime)
                set Page1.PlayClickAnim3 = 0.5
            else:
                set Page1.PlayClickAnim4 = 1.0
                Sleep(ClickPulseTime)
                set Page1.PlayClickAnim4 = 0.5
        else if(CurrentPage = 2):
            if(Index = 1):
                set Page2.PlayClickAnim1 = 1.0
                Sleep(ClickPulseTime)
                set Page2.PlayClickAnim1 = 0.5
            else if(Index = 2):
                set Page2.PlayClickAnim2 = 1.0
                Sleep(ClickPulseTime)
                set Page2.PlayClickAnim2 = 0.5
            else if(Index = 3):
                set Page2.PlayClickAnim3 = 1.0
                Sleep(ClickPulseTime)
                set Page2.PlayClickAnim3 = 0.5
            else:
                set Page2.PlayClickAnim4 = 1.0
                Sleep(ClickPulseTime)
                set Page2.PlayClickAnim4 = 0.5
        else:
            if(Index = 1):
                set Page3.PlayClickAnim1 = 1.0
                Sleep(ClickPulseTime)
                set Page3.PlayClickAnim1 = 0.5
            else if(Index = 2):
                set Page3.PlayClickAnim2 = 1.0
                Sleep(ClickPulseTime)
                set Page3.PlayClickAnim2 = 0.5
            else if(Index = 3):
                set Page3.PlayClickAnim3 = 1.0
                Sleep(ClickPulseTime)
                set Page3.PlayClickAnim3 = 0.5
            else:
                set Page3.PlayClickAnim4 = 1.0
                Sleep(ClickPulseTime)
                set Page3.PlayClickAnim4 = 0.5
    SetHoverExit(Value:float):void =
        if(Value = 1.0):
            ClearAllHoversExcept(5)
        if(CurrentPage = 1):
            set P1_HoverExit = Value
            set Page1.IsHoveringExit = Value
        else if(CurrentPage = 2):
            set P2_HoverExit = Value
            set Page2.IsHoveringExit = Value
        else:
            set P3_HoverExit = Value
            set Page3.IsHoveringExit = Value
    SetHoverBack(Value:float):void =
        if(Value = 1.0):
            ClearAllHoversExcept(6)
        if(CurrentPage = 1):
            set P1_HoverBack = 0.5
            set Page1.IsHoveringBack = 0.5
        else if(CurrentPage = 2):
            set P2_HoverBack = Value
            set Page2.IsHoveringBack = Value
        else:
            set P3_HoverBack = Value
            set Page3.IsHoveringBack = Value
    SetHoverNext(Value:float):void =
        if(Value = 1.0):
            ClearAllHoversExcept(7)
        if(CurrentPage = 1):
            set P1_HoverNext = Value
            set Page1.IsHoveringNext = Value
        else if(CurrentPage = 2):
            set P2_HoverNext = Value
            set Page2.IsHoveringNext = Value
        else:
            set P3_HoverNext = 0.5
            set Page3.IsHoveringNext = 0.5
    ApplyHoverToCurrentPage():void =
        if(CurrentPage = 1):
            set Page1.IsHovering1 = P1_HoverSkill1
            set Page1.IsHovering2 = P1_HoverSkill2
            set Page1.IsHovering3 = P1_HoverSkill3
            set Page1.IsHovering4 = P1_HoverSkill4
            set Page1.IsHoveringExit = P1_HoverExit
            set Page1.IsHoveringBack = P1_HoverBack
            if(P1_ResetNextOnReturn = true):
                set P1_ResetNextOnReturn = false
                set P1_HoverNext = 0.0
                set Page1.IsHoveringNext = 0.0
            else:
                set Page1.IsHoveringNext = P1_HoverNext
        else if(CurrentPage = 2):
            set Page2.IsHovering1 = P2_HoverSkill1
            set Page2.IsHovering2 = P2_HoverSkill2
            set Page2.IsHovering3 = P2_HoverSkill3
            set Page2.IsHovering4 = P2_HoverSkill4
            set Page2.IsHoveringExit = P2_HoverExit
            if(P2_ResetBackOnReturn = true):
                set P2_ResetBackOnReturn = false
                set P2_HoverBack = 0.0
                set Page2.IsHoveringBack = 0.0
            else:
                set Page2.IsHoveringBack = P2_HoverBack
            if(P2_ResetNextOnReturn = true):
                set P2_ResetNextOnReturn = false
                set P2_HoverNext = 0.0
                set Page2.IsHoveringNext = 0.0
            else:
                set Page2.IsHoveringNext = P2_HoverNext
        else:
            set Page3.IsHovering1 = P3_HoverSkill1
            set Page3.IsHovering2 = P3_HoverSkill2
            set Page3.IsHovering3 = P3_HoverSkill3
            set Page3.IsHovering4 = P3_HoverSkill4
            set Page3.IsHoveringExit = P3_HoverExit
            if(P3_ResetBackOnReturn = true):
                set P3_ResetBackOnReturn = false
                set P3_HoverBack = 0.0
                set Page3.IsHoveringBack = 0.0
            else:
                set Page3.IsHoveringBack = P3_HoverBack
            set Page3.IsHoveringNext = P3_HoverNext
    ResetPageAnimVars(PageIndex:int):void =
        if(PageIndex = 1):
            set P1_HoverSkill1 = 0.5
            set P1_HoverSkill2 = 0.5
            set P1_HoverSkill3 = 0.5
            set P1_HoverSkill4 = 0.5
            set P1_HoverExit = 0.5
            set P1_HoverBack = 0.5
            set P1_HoverNext = 0.5
            set Page1.IsHovering1 = 0.5
            set Page1.IsHovering2 = 0.5
            set Page1.IsHovering3 = 0.5
            set Page1.IsHovering4 = 0.5
            set Page1.IsHoveringExit = 0.5
            set Page1.IsHoveringBack = 0.5
            set Page1.IsHoveringNext = 0.5
            set Page1.PlayClickAnim1 = 0.5
            set Page1.PlayClickAnim2 = 0.5
            set Page1.PlayClickAnim3 = 0.5
            set Page1.PlayClickAnim4 = 0.5
        else if(PageIndex = 2):
            set P2_HoverSkill1 = 0.5
            set P2_HoverSkill2 = 0.5
            set P2_HoverSkill3 = 0.5
            set P2_HoverSkill4 = 0.5
            set P2_HoverExit = 0.5
            set P2_HoverBack = 0.5
            set P2_HoverNext = 0.5
            set Page2.IsHovering1 = 0.5
            set Page2.IsHovering2 = 0.5
            set Page2.IsHovering3 = 0.5
            set Page2.IsHovering4 = 0.5
            set Page2.IsHoveringExit = 0.5
            set Page2.IsHoveringBack = 0.5
            set Page2.IsHoveringNext = 0.5
            set Page2.PlayClickAnim1 = 0.5
            set Page2.PlayClickAnim2 = 0.5
            set Page2.PlayClickAnim3 = 0.5
            set Page2.PlayClickAnim4 = 0.5
        else:
            set P3_HoverSkill1 = 0.5
            set P3_HoverSkill2 = 0.5
            set P3_HoverSkill3 = 0.5
            set P3_HoverSkill4 = 0.5
            set P3_HoverExit = 0.5
            set P3_HoverBack = 0.5
            set P3_HoverNext = 0.5
            set Page3.IsHovering1 = 0.5
            set Page3.IsHovering2 = 0.5
            set Page3.IsHovering3 = 0.5
            set Page3.IsHovering4 = 0.5
            set Page3.IsHoveringExit = 0.5
            set Page3.IsHoveringBack = 0.5
            set Page3.IsHoveringNext = 0.5
            set Page3.PlayClickAnim1 = 0.5
            set Page3.PlayClickAnim2 = 0.5
            set Page3.PlayClickAnim3 = 0.5
            set Page3.PlayClickAnim4 = 0.5

    MakeSkillIcon(Tex:texture):widget =
        texture_block{
            DefaultImage := Tex,
            DefaultDesiredSize := vector2{X := IconSize, Y := IconSize}
        }

    MakePagingIcon(Tex:texture):widget =
        texture_block{
            DefaultImage := Tex,
            DefaultDesiredSize := vector2{X := PagingIconSize, Y := PagingIconSize}
        }
    AddIconToStackBox(Box:stack_box, Tex:texture):void =
        Icon := MakeSkillIcon(Tex)
        Box.AddWidget(
            stack_box_slot:
                Widget := Icon
                Padding := margin{Left := IconPadLeft, Top := 0.0, Right := 0.0, Bottom := 0.0}
                HorizontalAlignment := horizontal_alignment.Left
                VerticalAlignment := vertical_alignment.Center
        )

    # --- Skill Points Persistence ---

    ClampSkillPoints(Value:int):int =
        if(Value < 0):
            0
        else if(Value > 15):
            15
        else:
            Value

    MakeSkillPointsKey(PageIndex:int, SkillIndex:int):string =
        "{PersistencePrefix}_P{PageIndex}_S{SkillIndex}"

    GetSkillPointsCount(PageIndex:int, SkillIndex:int):int =
        if(PageIndex = 1):
            if(SkillIndex = 1):
                SkillPointsCount_P1_S1
            else if(SkillIndex = 2):
                SkillPointsCount_P1_S2
            else if(SkillIndex = 3):
                SkillPointsCount_P1_S3
            else:
                SkillPointsCount_P1_S4
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                SkillPointsCount_P2_S1
            else if(SkillIndex = 2):
                SkillPointsCount_P2_S2
            else if(SkillIndex = 3):
                SkillPointsCount_P2_S3
            else:
                SkillPointsCount_P2_S4
        else:
            if(SkillIndex = 1):
                SkillPointsCount_P3_S1
            else if(SkillIndex = 2):
                SkillPointsCount_P3_S2
            else if(SkillIndex = 3):
                SkillPointsCount_P3_S3
            else:
                SkillPointsCount_P3_S4

    SetSkillPointsCount(PageIndex:int, SkillIndex:int, NewValue:int):void =
        Clamped := ClampSkillPoints(NewValue)
        if(PageIndex = 1):
            if(SkillIndex = 1):
                set SkillPointsCount_P1_S1 = Clamped
            else if(SkillIndex = 2):
                set SkillPointsCount_P1_S2 = Clamped
            else if(SkillIndex = 3):
                set SkillPointsCount_P1_S3 = Clamped
            else:
                set SkillPointsCount_P1_S4 = Clamped
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                set SkillPointsCount_P2_S1 = Clamped
            else if(SkillIndex = 2):
                set SkillPointsCount_P2_S2 = Clamped
            else if(SkillIndex = 3):
                set SkillPointsCount_P2_S3 = Clamped
            else:
                set SkillPointsCount_P2_S4 = Clamped
        else:
            if(SkillIndex = 1):
                set SkillPointsCount_P3_S1 = Clamped
            else if(SkillIndex = 2):
                set SkillPointsCount_P3_S2 = Clamped
            else if(SkillIndex = 3):
                set SkillPointsCount_P3_S3 = Clamped
            else:
                set SkillPointsCount_P3_S4 = Clamped

    GetSkillPointsBox(PageIndex:int, SkillIndex:int):stack_box =
        if(PageIndex = 1):
            if(SkillIndex = 1):
                SkillPointsBox_P1_S1
            else if(SkillIndex = 2):
                SkillPointsBox_P1_S2
            else if(SkillIndex = 3):
                SkillPointsBox_P1_S3
            else:
                SkillPointsBox_P1_S4
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                SkillPointsBox_P2_S1
            else if(SkillIndex = 2):
                SkillPointsBox_P2_S2
            else if(SkillIndex = 3):
                SkillPointsBox_P2_S3
            else:
                SkillPointsBox_P2_S4
        else:
            if(SkillIndex = 1):
                SkillPointsBox_P3_S1
            else if(SkillIndex = 2):
                SkillPointsBox_P3_S2
            else if(SkillIndex = 3):
                SkillPointsBox_P3_S3
            else:
                SkillPointsBox_P3_S4
    
    GetSkillTextBlock(PageIndex:int, SkillIndex:int):text_block=
        if(PageIndex = 1):
            if(SkillIndex = 1):
                TB_P1_S1
            else if(SkillIndex = 2):
                TB_P1_S2
            else if(SkillIndex = 3):
                TB_P1_S3
            else:
                TB_P1_S4
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                TB_P2_S1
            else if(SkillIndex = 2):
                TB_P2_S2
            else if(SkillIndex = 3):
                TB_P2_S3
            else:
                TB_P2_S4
        else:
            if(SkillIndex = 1):
                TB_P3_S1
            else if(SkillIndex = 2):
                TB_P3_S2
            else if(SkillIndex = 3):
                TB_P3_S3
            else:
                TB_P3_S4

    # --- COST TEXT BLOCK GETTER ---
    GetCostTextBlock(PageIndex:int, SkillIndex:int):text_block=
        if(PageIndex = 1):
            if(SkillIndex = 1):
                TB_Cost_P1_S1
            else if(SkillIndex = 2):
                TB_Cost_P1_S2
            else if(SkillIndex = 3):
                TB_Cost_P1_S3
            else:
                TB_Cost_P1_S4
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                TB_Cost_P2_S1
            else if(SkillIndex = 2):
                TB_Cost_P2_S2
            else if(SkillIndex = 3):
                TB_Cost_P2_S3
            else:
                TB_Cost_P2_S4
        else:
            if(SkillIndex = 1):
                TB_Cost_P3_S1
            else if(SkillIndex = 2):
                TB_Cost_P3_S2
            else if(SkillIndex = 3):
                TB_Cost_P3_S3
            else:
                TB_Cost_P3_S4

    UpdateSkillText(PageIndex:int, SkillIndex:int):void=
        TB := GetSkillTextBlock(PageIndex, SkillIndex)
        Count := GetSkillPointsCount(PageIndex, SkillIndex)
        TB.SetText(LocalStringToMessage("{Count}/15"))
        # Update Cost text as well whenever level changes
        UpdateCostText(PageIndex, SkillIndex)

    UpdateCostText(PageIndex:int, SkillIndex:int):void=
        TBC := GetCostTextBlock(PageIndex, SkillIndex)
        CurrentLevel := GetSkillPointsCount(PageIndex, SkillIndex)
        
        if(CurrentLevel >= 15):
            TBC.SetText(LocalStringToMessage("MAX"))
        else:
            CostOpt := GetCostForNextLevel(PageIndex, SkillIndex, CurrentLevel)
            if(Cost := CostOpt?):
                # Display price amount
                # Assumes amount is Integer or simple float. Formatting minimal.
                Val := Cost.Amount
                if(Val = 0.0):
                    TBC.SetText(LocalStringToMessage("Free"))
                else:
                    TBC.SetText(LocalStringToMessage("{Val}"))
            else:
                TBC.SetText(LocalStringToMessage("Free"))

    LocalStringToMessage<localizes>(S:string):message = "{S}"

    SaveSkillPointsCount(PageIndex:int, SkillIndex:int):void =
        if(OwnerP := Owner?, RM := ResourceManager?):
            Key := MakeSkillPointsKey(PageIndex, SkillIndex)
            Count := GetSkillPointsCount(PageIndex, SkillIndex)
            PMBase := RM.PersistenceManager
            if(PM := Resource_Persistence_Manager[PMBase]):
                PM.RecordNewResourcePersistence(OwnerP, Key, Count * 1.0, 1.0, 0.0, true)

    LoadSkillPointsCountsFromPersistence():void =
        # Defaults
        set SkillPointsCount_P1_S1 = 0
        set SkillPointsCount_P1_S2 = 0
        set SkillPointsCount_P1_S3 = 0
        set SkillPointsCount_P1_S4 = 0
        set SkillPointsCount_P2_S1 = 0
        set SkillPointsCount_P2_S2 = 0
        set SkillPointsCount_P2_S3 = 0
        set SkillPointsCount_P2_S4 = 0
        set SkillPointsCount_P3_S1 = 0
        set SkillPointsCount_P3_S2 = 0
        set SkillPointsCount_P3_S3 = 0
        set SkillPointsCount_P3_S4 = 0

        if(OwnerP := Owner?, RM := ResourceManager?):
            RM.PersistenceManager.InitializePlayer(OwnerP)

            K11 := MakeSkillPointsKey(1, 1)
            K12 := MakeSkillPointsKey(1, 2)
            K13 := MakeSkillPointsKey(1, 3)
            K14 := MakeSkillPointsKey(1, 4)
            K21 := MakeSkillPointsKey(2, 1)
            K22 := MakeSkillPointsKey(2, 2)
            K23 := MakeSkillPointsKey(2, 3)
            K24 := MakeSkillPointsKey(2, 4)
            K31 := MakeSkillPointsKey(3, 1)
            K32 := MakeSkillPointsKey(3, 2)
            K33 := MakeSkillPointsKey(3, 3)
            K34 := MakeSkillPointsKey(3, 4)

            if(PlayerStats := RM.PersistenceManager.GetPlayerStats[OwnerP]):
                for(Idx -> Saved : PlayerStats.ResourceCollection):
                    Name := Saved.ResourceName
                    if(Name = K11):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P1_S1 = ClampSkillPoints(I)
                    else if(Name = K12):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P1_S2 = ClampSkillPoints(I)
                    else if(Name = K13):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P1_S3 = ClampSkillPoints(I)
                    else if(Name = K14):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P1_S4 = ClampSkillPoints(I)
                    else if(Name = K21):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P2_S1 = ClampSkillPoints(I)
                    else if(Name = K22):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P2_S2 = ClampSkillPoints(I)
                    else if(Name = K23):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P2_S3 = ClampSkillPoints(I)
                    else if(Name = K24):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P2_S4 = ClampSkillPoints(I)
                    else if(Name = K31):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P3_S1 = ClampSkillPoints(I)
                    else if(Name = K32):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P3_S2 = ClampSkillPoints(I)
                    else if(Name = K33):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P3_S3 = ClampSkillPoints(I)
                    else if(Name = K34):
                        if(I := Int[Saved.ResourceCount]):
                            set SkillPointsCount_P3_S4 = ClampSkillPoints(I)

    RebuildSkillPointsIconsFromCounts():void =
        # Update texts
        UpdateSkillText(1,1); UpdateSkillText(1,2); UpdateSkillText(1,3); UpdateSkillText(1,4)
        UpdateSkillText(2,1); UpdateSkillText(2,2); UpdateSkillText(2,3); UpdateSkillText(2,4)
        UpdateSkillText(3,1); UpdateSkillText(3,2); UpdateSkillText(3,3); UpdateSkillText(3,4)

        if(Tex := SkillPoint?):
            # Page 1
            var I : int = 0
            loop:
                if(I >= SkillPointsCount_P1_S1):
                    break
                AddIconToStackBox(SkillPointsBox_P1_S1, Tex)
                set I += 1
            var I2 : int = 0
            loop:
                if(I2 >= SkillPointsCount_P1_S2):
                    break
                AddIconToStackBox(SkillPointsBox_P1_S2, Tex)
                set I2 += 1
            var I3 : int = 0
            loop:
                if(I3 >= SkillPointsCount_P1_S3):
                    break
                AddIconToStackBox(SkillPointsBox_P1_S3, Tex)
                set I3 += 1
            var I4 : int = 0
            loop:
                if(I4 >= SkillPointsCount_P1_S4):
                    break
                AddIconToStackBox(SkillPointsBox_P1_S4, Tex)
                set I4 += 1

            # Page 2
            var J : int = 0
            loop:
                if(J >= SkillPointsCount_P2_S1):
                    break
                AddIconToStackBox(SkillPointsBox_P2_S1, Tex)
                set J += 1
            var J2 : int = 0
            loop:
                if(J2 >= SkillPointsCount_P2_S2):
                    break
                AddIconToStackBox(SkillPointsBox_P2_S2, Tex)
                set J2 += 1
            var J3 : int = 0
            loop:
                if(J3 >= SkillPointsCount_P2_S3):
                    break
                AddIconToStackBox(SkillPointsBox_P2_S3, Tex)
                set J3 += 1
            var J4 : int = 0
            loop:
                if(J4 >= SkillPointsCount_P2_S4):
                    break
                AddIconToStackBox(SkillPointsBox_P2_S4, Tex)
                set J4 += 1

            # Page 3
            var K : int = 0
            loop:
                if(K >= SkillPointsCount_P3_S1):
                    break
                AddIconToStackBox(SkillPointsBox_P3_S1, Tex)
                set K += 1
            var K2 : int = 0
            loop:
                if(K2 >= SkillPointsCount_P3_S2):
                    break
                AddIconToStackBox(SkillPointsBox_P3_S2, Tex)
                set K2 += 1
            var K3 : int = 0
            loop:
                if(K3 >= SkillPointsCount_P3_S3):
                    break
                AddIconToStackBox(SkillPointsBox_P3_S3, Tex)
                set K3 += 1
            var K4 : int = 0
            loop:
                if(K4 >= SkillPointsCount_P3_S4):
                    break
                AddIconToStackBox(SkillPointsBox_P3_S4, Tex)
                set K4 += 1

    AddSkillPointToBox(Index:int):void =
        if(Tex := SkillPoint?):
            Count := GetSkillPointsCount(CurrentPage, Index)
            if(Count >= 15):
                return
            Box := GetSkillPointsBox(CurrentPage, Index)
            AddIconToStackBox(Box, Tex)
            SetSkillPointsCount(CurrentPage, Index, Count + 1)
            SaveSkillPointsCount(CurrentPage, Index)
            UpdateSkillText(CurrentPage, Index)

    # ==========================================================================
    #  HELPER: Get price for the NEXT level
    # ==========================================================================
    GetCostForNextLevel(PageIndex:int, SkillIndex:int, CurrentLevel:int):?SpendableResource=
        # Get correct config object
        var Cfg : Skill_Price_Config = Skill_Price_Config{}

        if(PageIndex = 1):
            if(SkillIndex = 1):
                set Cfg = P1_S1_Cost
            else if(SkillIndex = 2):
                set Cfg = P1_S2_Cost
            else if(SkillIndex = 3):
                set Cfg = P1_S3_Cost
            else:
                set Cfg = P1_S4_Cost
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                set Cfg = P2_S1_Cost
            else if(SkillIndex = 2):
                set Cfg = P2_S2_Cost
            else if(SkillIndex = 3):
                set Cfg = P2_S3_Cost
            else:
                set Cfg = P2_S4_Cost
        else:
            if(SkillIndex = 1):
                set Cfg = P3_S1_Cost
            else if(SkillIndex = 2):
                set Cfg = P3_S2_Cost
            else if(SkillIndex = 3):
                set Cfg = P3_S3_Cost
            else:
                set Cfg = P3_S4_Cost

        # Check if price exists for current level
        if(CurrentLevel < Cfg.LevelPrices.Length):
            if(Price := Cfg.LevelPrices[CurrentLevel]):
                return option{Price}
        
        return false

    # ==========================================================================
    #  HELPER: Get reward for the NEW level (CurrentCount + 1 is passed usually as logic is post-increment?
    #  No, we use current count index. If I have 0, I buy level 1 (index 0).
    # ==========================================================================
    GetRewardForLevel(PageIndex:int, SkillIndex:int, LevelIndex:int):?GainableResource=
        var Rew : Skill_Reward_Config = Skill_Reward_Config{}
        if(PageIndex = 1):
            if(SkillIndex = 1):
                set Rew = P1_S1_Reward
            else if(SkillIndex = 2):
                set Rew = P1_S2_Reward
            else if(SkillIndex = 3):
                set Rew = P1_S3_Reward
            else:
                set Rew = P1_S4_Reward
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                set Rew = P2_S1_Reward
            else if(SkillIndex = 2):
                set Rew = P2_S2_Reward
            else if(SkillIndex = 3):
                set Rew = P2_S3_Reward
            else:
                set Rew = P2_S4_Reward
        else:
            if(SkillIndex = 1):
                set Rew = P3_S1_Reward
            else if(SkillIndex = 2):
                set Rew = P3_S2_Reward
            else if(SkillIndex = 3):
                set Rew = P3_S3_Reward
            else:
                set Rew = P3_S4_Reward

        if(LevelIndex < Rew.LevelRewards.Length):
            if(R := Rew.LevelRewards[LevelIndex]):
                return option{R}
        return false

    HandleSkillButton(Index:int, B:button)<suspends>:void =
        race:
            MenuCloseEvent.Await()

            loop:
                # Получаем сообщение и извлекаем агента
                Message := B.OnClick().Await()
                Agent := Message.Player
                
                Enabled := IsHitEnabled(CurrentPage, Index)
                if(Enabled = true):
                    # --- CHECK PAYMENT & REWARD ---
                    var CanBuy : logic = false
                    
                    CurrentCount := GetSkillPointsCount(CurrentPage, Index)
                    
                    if(CurrentCount >= 15):
                        FailedSound.Play(Agent)
                    else:
                        CostOpt := GetCostForNextLevel(CurrentPage, Index, CurrentCount)
                        
                        # Payment Logic
                        if(Cost := CostOpt?):
                            if(RM := ResourceManager?):
                                if(PR := RM.PlayersMap[Agent]):
                                    HasEnough := PR.Check(array{Cost})
                                    if(HasEnough?):
                                        PR.Spend(array{Cost})
                                        PurchaseSound.Play(Agent)
                                        set CanBuy = true
                                    else:
                                        FailedSound.Play(Agent)
                                else:
                                    FailedSound.Play(Agent)
                            else:
                                FailedSound.Play(Agent)
                        else:
                            # Free
                            PurchaseSound.Play(Agent)
                            set CanBuy = true

                    if(CanBuy?):
                        # Grant Reward
                        RewardOpt := GetRewardForLevel(CurrentPage, Index, CurrentCount)
                        # Переименовали переменную Reward -> GrantReward чтобы убрать конфликт
                        if(GrantReward := RewardOpt?, RM := ResourceManager?, PR := RM.PlayersMap[Agent]):
                             # ИСПРАВЛЕНО: Используем Collect вместо AddResource
                             PR.Collect(array{GrantReward})
                        
                        AddSkillPointToBox(Index)
                        spawn{PulseClick(Index)}

            loop:
                B.HighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    Enabled := IsHitEnabled(CurrentPage, Index)
                    if(Enabled = true):
                        RequestHoverOn(Index)

            loop:
                B.UnhighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    Enabled := IsHitEnabled(CurrentPage, Index)
                    if(Enabled = true):
                        RequestHoverOff(Index)

    HandleExitButton(B:button)<suspends>:void =
        race:
            MenuCloseEvent.Await()

            loop:
                B.OnClick().Await()
                ExitEvent.Signal()

            loop:
                B.HighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOn(5)

            loop:
                B.UnhighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOff(5)

    HandleBackButton(B:button)<suspends>:void =
        race:
            MenuCloseEvent.Await()

            loop:
                B.OnClick().Await()
                if(CurrentPage = 2 or CurrentPage = 3):
                    PrevPageEvent.Signal()

            loop:
                B.HighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOn(6)

            loop:
                B.UnhighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOff(6)

    HandleNextButton(B:button)<suspends>:void =
        race:
            MenuCloseEvent.Await()

            loop:
                B.OnClick().Await()
                if(CurrentPage = 1 or CurrentPage = 2):
                    NextPageEvent.Signal()

            loop:
                B.HighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOn(7)

            loop:
                B.UnhighlightEvent().Await()
                if(HoverTransitionLocked = false):
                    RequestHoverOff(7)

    RunCurrencyUpdateLoop()<suspends>:void=
        if(RM := ResourceManager?, OwnerP := Owner?):
            loop:
                Sleep(0.5)
                # Update currency display
                if(TargetCurrencyName <> ""):
                    if(PR := RM.PlayersMap[OwnerP]):
                        Res := PR.GetResource(TargetCurrencyName)
                        Amt := Res.ResourceAmount
                        TB_PlayerCurrency.SetText(LocalStringToMessage("{Amt}"))


button_manager_device := class(creative_device):

    @editable
    OpenMenuButton : button_device = button_device{}

    @editable
    ResetButton : button_device = button_device{}

    @editable
    SkillPoint : ?texture = false

    # --- Persistence via MapAcademy ResourceManager ---
    @editable
    ResourceManagerDevice : ?Resource_Manager = false

    @editable
    SkillPointsPersistencePrefix : string = "SKMENU"

    @editable
    CurrencyName : string = "Money"

    # Sounds
    @editable
    PurchaseSound : audio_player_device = audio_player_device{}

    @editable
    FailedSound : audio_player_device = audio_player_device{}

    # --- CONFIGS FOR SKILL COSTS ---
    @editable P1_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P1_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P1_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P1_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    @editable P2_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P2_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P2_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P2_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    @editable P3_S1_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P3_S2_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P3_S3_Cost : Skill_Price_Config = Skill_Price_Config{}
    @editable P3_S4_Cost : Skill_Price_Config = Skill_Price_Config{}

    # --- CONFIGS FOR SKILL REWARDS ---
    @editable P1_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P1_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P1_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P1_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    @editable P2_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P2_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P2_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P2_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    @editable P3_S1_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P3_S2_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P3_S3_Reward : Skill_Reward_Config = Skill_Reward_Config{}
    @editable P3_S4_Reward : Skill_Reward_Config = Skill_Reward_Config{}

    # --- Icons ---
    @editable
    NoPrevPageIcon : ?texture = false

    @editable
    NoNextPageIcon : ?texture = false

    # --- Enable / Disable hit1-hit4 for each page (true = enabled) ---
    @editable
    EnableP1Hit1 : logic = true
    @editable
    EnableP1Hit2 : logic = true
    @editable
    EnableP1Hit3 : logic = true
    @editable
    EnableP1Hit4 : logic = true

    @editable
    EnableP2Hit1 : logic = true
    @editable
    EnableP2Hit2 : logic = true
    @editable
    EnableP2Hit3 : logic = true
    @editable
    EnableP2Hit4 : logic = true

    @editable
    EnableP3Hit1 : logic = true
    @editable
    EnableP3Hit2 : logic = true
    @editable
    EnableP3Hit3 : logic = true
    @editable
    EnableP3Hit4 : logic = true

    OnBegin<override>()<suspends>:void =
        OpenMenuButton.InteractedWithEvent.Subscribe(Proxy)
        ResetButton.InteractedWithEvent.Subscribe(HandleReset)

    Proxy(Agent:agent):void =
        spawn{HandleMenu(Agent)}

    # =========================================================
    #  RESET LOGIC
    # =========================================================
    HandleReset(Agent:agent):void=
        spawn{ResetSkills(Agent)}

    GetConfigFor(Page:int, Skill:int):tuple(Skill_Price_Config, Skill_Reward_Config)=
        if(Page=1):
            if(Skill=1):
                return (P1_S1_Cost, P1_S1_Reward)
            else if(Skill=2):
                return (P1_S2_Cost, P1_S2_Reward)
            else if(Skill=3):
                return (P1_S3_Cost, P1_S3_Reward)
            else:
                return (P1_S4_Cost, P1_S4_Reward)
        else if(Page=2):
            if(Skill=1):
                return (P2_S1_Cost, P2_S1_Reward)
            else if(Skill=2):
                return (P2_S2_Cost, P2_S2_Reward)
            else if(Skill=3):
                return (P2_S3_Cost, P2_S3_Reward)
            else:
                return (P2_S4_Cost, P2_S4_Reward)
        else:
            if(Skill=1):
                return (P3_S1_Cost, P3_S1_Reward)
            else if(Skill=2):
                return (P3_S2_Cost, P3_S2_Reward)
            else if(Skill=3):
                return (P3_S3_Cost, P3_S3_Reward)
            else:
                return (P3_S4_Cost, P3_S4_Reward)

    # Хелпер для конвертации цены (Spendable) в ресурс для возврата (Gainable)
    MakeRefundResource(S:SpendableResource):GainableResource=
        GainableResource{ ResourceName := S.ResourceName, Amount := S.Amount }

    ResetSkills(Agent:agent)<suspends>:void=
        if(RM := ResourceManagerDevice?, OwnerP := player[Agent]):
            RM.PersistenceManager.InitializePlayer(OwnerP)
            
            var TotalRefund : []GainableResource = array{}
            var TotalRemove : []SpendableResource = array{}

            # Iterate all possible skills (3 pages * 4 skills)
            # Cast base manager to resource persistence manager
            PMBase := RM.PersistenceManager
            if(PM := Resource_Persistence_Manager[PMBase]):

                if(PlayerStats := PM.GetPlayerStats[OwnerP]):
                    # Loop Pages
                    for(P := 1..3):
                        # Loop Skills
                        for(S := 1..4):
                            Key := "{SkillPointsPersistencePrefix}_P{P}_S{S}"
                            
                            var Count : int = 0
                            # Find count in persistence
                            for(Idx -> Saved : PlayerStats.ResourceCollection):
                                 if(Saved.ResourceName = Key, I := Int[Saved.ResourceCount]):
                                     set Count = I
                            
                            if(Count > 0):
                                if(Count > 15): 
                                    set Count = 15
                                
                                # Calculate Refund for this skill
                                Configs := GetConfigFor(P, S)
                                PriceCfg := Configs(0)
                                RewardCfg := Configs(1)
                                
                                var Lvl : int = 0
                                loop:
                                    if(Lvl >= Count):
                                        break
                                    
                                    # Refund Prices
                                    if(Lvl < PriceCfg.LevelPrices.Length):
                                        if(Res := PriceCfg.LevelPrices[Lvl]):
                                            set TotalRefund = TotalRefund + array{ MakeRefundResource(Res) }
                                    
                                    # Clawback Rewards
                                    if(Lvl < RewardCfg.LevelRewards.Length):
                                        if(Rew := RewardCfg.LevelRewards[Lvl]):
                                            # We need to SPEND this resource to remove it
                                            SpendReq := SpendableResource{ ResourceName := Rew.ResourceName, Amount := Rew.Amount }
                                            set TotalRemove = TotalRemove + array{ SpendReq }

                                    set Lvl += 1
                                    
                                # Reset Persistence to 0
                                PM.RecordNewResourcePersistence(OwnerP, Key, 0.0, 1.0, 0.0, true)

            if(PR := RM.PlayersMap[Agent]):
                # Process Refund (Give back cost)
                if(TotalRefund.Length > 0):
                    PR.Collect(TotalRefund)
                
                # Process Clawback (Take back reward)
                if(TotalRemove.Length > 0):
                    PR.Spend(TotalRemove)

    # ... (Rest of UI Logic) ...
    # (Existing UI NeutralizeSkillVars code unchanged)
    NeutralizeSkillVars(Menu:skills_menu, PageIndex:int, SkillIndex:int):void =
        if(PageIndex = 1):
            if(SkillIndex = 1):
                set Menu.P1_HoverSkill1 = 0.5
                set Menu.Page1.IsHovering1 = 0.5
                set Menu.Page1.PlayClickAnim1 = 0.5
            else if(SkillIndex = 2):
                set Menu.P1_HoverSkill2 = 0.5
                set Menu.Page1.IsHovering2 = 0.5
                set Menu.Page1.PlayClickAnim2 = 0.5
            else if(SkillIndex = 3):
                set Menu.P1_HoverSkill3 = 0.5
                set Menu.Page1.IsHovering3 = 0.5
                set Menu.Page1.PlayClickAnim3 = 0.5
            else:
                set Menu.P1_HoverSkill4 = 0.5
                set Menu.Page1.IsHovering4 = 0.5
                set Menu.Page1.PlayClickAnim4 = 0.5
        else if(PageIndex = 2):
            if(SkillIndex = 1):
                set Menu.P2_HoverSkill1 = 0.5
                set Menu.Page2.IsHovering1 = 0.5
                set Menu.Page2.PlayClickAnim1 = 0.5
            else if(SkillIndex = 2):
                set Menu.P2_HoverSkill2 = 0.5
                set Menu.Page2.IsHovering2 = 0.5
                set Menu.Page2.PlayClickAnim2 = 0.5
            else if(SkillIndex = 3):
                set Menu.P2_HoverSkill3 = 0.5
                set Menu.Page2.IsHovering3 = 0.5
                set Menu.Page2.PlayClickAnim3 = 0.5
            else:
                set Menu.P2_HoverSkill4 = 0.5
                set Menu.Page2.IsHovering4 = 0.5
                set Menu.Page2.PlayClickAnim4 = 0.5
        else:
            if(SkillIndex = 1):
                set Menu.P3_HoverSkill1 = 0.5
                set Menu.Page3.IsHovering1 = 0.5
                set Menu.Page3.PlayClickAnim1 = 0.5
            else if(SkillIndex = 2):
                set Menu.P3_HoverSkill2 = 0.5
                set Menu.Page3.IsHovering2 = 0.5
                set Menu.Page3.PlayClickAnim2 = 0.5
            else if(SkillIndex = 3):
                set Menu.P3_HoverSkill3 = 0.5
                set Menu.Page3.IsHovering3 = 0.5
                set Menu.Page3.PlayClickAnim3 = 0.5
            else:
                set Menu.P3_HoverSkill4 = 0.5
                set Menu.Page3.IsHovering4 = 0.5
                set Menu.Page3.PlayClickAnim4 = 0.5

    ApplyPageVisibility(Menu:skills_menu, PageIndex:int, NoPrevW:?widget, NoNextW:?widget, Hit1:widget, Hit2:widget, Hit3:widget, Hit4:widget):void =
        # страницы
        if(PageIndex = 1):
            Menu.Page1.SetVisibility(widget_visibility.Visible)
            Menu.Page2.SetVisibility(widget_visibility.Collapsed)
            Menu.Page3.SetVisibility(widget_visibility.Collapsed)
        else if(PageIndex = 2):
            Menu.Page1.SetVisibility(widget_visibility.Collapsed)
            Menu.Page2.SetVisibility(widget_visibility.Visible)
            Menu.Page3.SetVisibility(widget_visibility.Collapsed)
        else:
            Menu.Page1.SetVisibility(widget_visibility.Collapsed)
            Menu.Page2.SetVisibility(widget_visibility.Collapsed)
            Menu.Page3.SetVisibility(widget_visibility.Visible)

        # stack_box, ТЕКСТА (счетчик) и ТЕКСТА (цена)
        if(PageIndex = 1):
            Menu.SkillPointsBox_P1_S1.SetVisibility(widget_visibility.Visible); Menu.TB_P1_S1.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P1_S1.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P1_S2.SetVisibility(widget_visibility.Visible); Menu.TB_P1_S2.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P1_S2.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P1_S3.SetVisibility(widget_visibility.Visible); Menu.TB_P1_S3.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P1_S3.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P1_S4.SetVisibility(widget_visibility.Visible); Menu.TB_P1_S4.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P1_S4.SetVisibility(widget_visibility.Visible)

            Menu.SkillPointsBox_P2_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S4.SetVisibility(widget_visibility.Collapsed)

            Menu.SkillPointsBox_P3_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S4.SetVisibility(widget_visibility.Collapsed)
        else if(PageIndex = 2):
            Menu.SkillPointsBox_P1_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S4.SetVisibility(widget_visibility.Collapsed)

            Menu.SkillPointsBox_P2_S1.SetVisibility(widget_visibility.Visible); Menu.TB_P2_S1.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P2_S1.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P2_S2.SetVisibility(widget_visibility.Visible); Menu.TB_P2_S2.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P2_S2.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P2_S3.SetVisibility(widget_visibility.Visible); Menu.TB_P2_S3.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P2_S3.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P2_S4.SetVisibility(widget_visibility.Visible); Menu.TB_P2_S4.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P2_S4.SetVisibility(widget_visibility.Visible)

            Menu.SkillPointsBox_P3_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P3_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P3_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P3_S4.SetVisibility(widget_visibility.Collapsed)
        else:
            Menu.SkillPointsBox_P1_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P1_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P1_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P1_S4.SetVisibility(widget_visibility.Collapsed)

            Menu.SkillPointsBox_P2_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S1.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S1.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S2.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S2.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S3.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S3.SetVisibility(widget_visibility.Collapsed)
            Menu.SkillPointsBox_P2_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_P2_S4.SetVisibility(widget_visibility.Collapsed); Menu.TB_Cost_P2_S4.SetVisibility(widget_visibility.Collapsed)

            Menu.SkillPointsBox_P3_S1.SetVisibility(widget_visibility.Visible); Menu.TB_P3_S1.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P3_S1.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P3_S2.SetVisibility(widget_visibility.Visible); Menu.TB_P3_S2.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P3_S2.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P3_S3.SetVisibility(widget_visibility.Visible); Menu.TB_P3_S3.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P3_S3.SetVisibility(widget_visibility.Visible)
            Menu.SkillPointsBox_P3_S4.SetVisibility(widget_visibility.Visible); Menu.TB_P3_S4.SetVisibility(widget_visibility.Visible); Menu.TB_Cost_P3_S4.SetVisibility(widget_visibility.Visible)

        # hit1-hit4 + FULL DISABLE if EnablePNHitN is false
        Hit1Enabled := Menu.IsHitEnabled(PageIndex, 1)
        if(Hit1Enabled = true):
            Hit1.SetVisibility(widget_visibility.Visible)
        else:
            # Отключаем всё: кнопку, стек бокс, счетчик, цену
            Hit1.SetVisibility(widget_visibility.Collapsed)
            if(PageIndex = 1):
                Menu.SkillPointsBox_P1_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P1_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P1_S1.SetVisibility(widget_visibility.Collapsed)
            else if(PageIndex = 2):
                Menu.SkillPointsBox_P2_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P2_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P2_S1.SetVisibility(widget_visibility.Collapsed)
            else:
                Menu.SkillPointsBox_P3_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P3_S1.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P3_S1.SetVisibility(widget_visibility.Collapsed)
            NeutralizeSkillVars(Menu, PageIndex, 1)

        Hit2Enabled := Menu.IsHitEnabled(PageIndex, 2)
        if(Hit2Enabled = true):
            Hit2.SetVisibility(widget_visibility.Visible)
        else:
            Hit2.SetVisibility(widget_visibility.Collapsed)
            if(PageIndex = 1):
                Menu.SkillPointsBox_P1_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P1_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P1_S2.SetVisibility(widget_visibility.Collapsed)
            else if(PageIndex = 2):
                Menu.SkillPointsBox_P2_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P2_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P2_S2.SetVisibility(widget_visibility.Collapsed)
            else:
                Menu.SkillPointsBox_P3_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P3_S2.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P3_S2.SetVisibility(widget_visibility.Collapsed)
            NeutralizeSkillVars(Menu, PageIndex, 2)

        Hit3Enabled := Menu.IsHitEnabled(PageIndex, 3)
        if(Hit3Enabled = true):
            Hit3.SetVisibility(widget_visibility.Visible)
        else:
            Hit3.SetVisibility(widget_visibility.Collapsed)
            if(PageIndex = 1):
                Menu.SkillPointsBox_P1_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P1_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P1_S3.SetVisibility(widget_visibility.Collapsed)
            else if(PageIndex = 2):
                Menu.SkillPointsBox_P2_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P2_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P2_S3.SetVisibility(widget_visibility.Collapsed)
            else:
                Menu.SkillPointsBox_P3_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P3_S3.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P3_S3.SetVisibility(widget_visibility.Collapsed)
            NeutralizeSkillVars(Menu, PageIndex, 3)

        Hit4Enabled := Menu.IsHitEnabled(PageIndex, 4)
        if(Hit4Enabled = true):
            Hit4.SetVisibility(widget_visibility.Visible)
        else:
            Hit4.SetVisibility(widget_visibility.Collapsed)
            if(PageIndex = 1):
                Menu.SkillPointsBox_P1_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P1_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P1_S4.SetVisibility(widget_visibility.Collapsed)
            else if(PageIndex = 2):
                Menu.SkillPointsBox_P2_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P2_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P2_S4.SetVisibility(widget_visibility.Collapsed)
            else:
                Menu.SkillPointsBox_P3_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_P3_S4.SetVisibility(widget_visibility.Collapsed)
                Menu.TB_Cost_P3_S4.SetVisibility(widget_visibility.Collapsed)
            NeutralizeSkillVars(Menu, PageIndex, 4)

        # иконки
        if(W := NoPrevW?):
            if(PageIndex = 1):
                W.SetVisibility(widget_visibility.Visible)
            else:
                W.SetVisibility(widget_visibility.Collapsed)

        if(W := NoNextW?):
            if(PageIndex = 3):
                W.SetVisibility(widget_visibility.Visible)
            else:
                W.SetVisibility(widget_visibility.Collapsed)


    HandleMenu(Agent:agent)<suspends>:void =
        if:
            Player := player[Agent]
            PlayerUI := GetPlayerUI[Player]
        then:
            Menu := skills_menu{
                Owner := option{Player}
                ResourceManager := ResourceManagerDevice
                PersistencePrefix := SkillPointsPersistencePrefix
                SkillPoint := SkillPoint
                TargetCurrencyName := CurrencyName

                # Sounds
                PurchaseSound := PurchaseSound
                FailedSound := FailedSound

                # Price configs
                P1_S1_Cost := P1_S1_Cost; P1_S2_Cost := P1_S2_Cost; P1_S3_Cost := P1_S3_Cost; P1_S4_Cost := P1_S4_Cost
                P2_S1_Cost := P2_S1_Cost; P2_S2_Cost := P2_S2_Cost; P2_S3_Cost := P2_S3_Cost; P2_S4_Cost := P2_S4_Cost
                P3_S1_Cost := P3_S1_Cost; P3_S2_Cost := P3_S2_Cost; P3_S3_Cost := P3_S3_Cost; P3_S4_Cost := P3_S4_Cost

                # Reward configs
                P1_S1_Reward := P1_S1_Reward; P1_S2_Reward := P1_S2_Reward; P1_S3_Reward := P1_S3_Reward; P1_S4_Reward := P1_S4_Reward
                P2_S1_Reward := P2_S1_Reward; P2_S2_Reward := P2_S2_Reward; P2_S3_Reward := P2_S3_Reward; P2_S4_Reward := P2_S4_Reward
                P3_S1_Reward := P3_S1_Reward; P3_S2_Reward := P3_S2_Reward; P3_S3_Reward := P3_S3_Reward; P3_S4_Reward := P3_S4_Reward

                P1_EnableHit1 := EnableP1Hit1; P1_EnableHit2 := EnableP1Hit2; P1_EnableHit3 := EnableP1Hit3; P1_EnableHit4 := EnableP1Hit4
                P2_EnableHit1 := EnableP2Hit1; P2_EnableHit2 := EnableP2Hit2; P2_EnableHit3 := EnableP2Hit3; P2_EnableHit4 := EnableP2Hit4
                P3_EnableHit1 := EnableP3Hit1; P3_EnableHit2 := EnableP3Hit2; P3_EnableHit3 := EnableP3Hit3; P3_EnableHit4 := EnableP3Hit4
            }

            Menu.LoadSkillPointsCountsFromPersistence()
            Menu.RebuildSkillPointsIconsFromCounts()

            var PageIndex : int = 1
            set Menu.CurrentPage = PageIndex

            # Hitboxes
            Hit1 := Menu.MakeHitButton1()
            Hit2 := Menu.MakeHitButton2()
            Hit3 := Menu.MakeHitButton3()
            Hit4 := Menu.MakeHitButton4()

            HitExit := Menu.MakeHitExitButton()
            HitBack := Menu.MakeHitBackButton()
            HitNext := Menu.MakeHitNextButton()

            var Slots : []canvas_slot = array{}

            # Pages
            set Slots += array:
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := true
                    ZOrder := 0
                    Widget := Menu.Page1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := true
                    ZOrder := 0
                    Widget := Menu.Page2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := true
                    ZOrder := 0
                    Widget := Menu.Page3

            # Navigation Hits
            set Slots += array:
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 0.0, Top := 365.983856, Right := 934.849365, Bottom := 73.806953}
                    ZOrder := 1000
                    Widget := HitExit
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 519.848572, Top := -1.90121, Right := 70.244476, Bottom := 70.241013}
                    ZOrder := 1000
                    Widget := HitBack
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 519.848572, Top := 123.608482, Right := 70.244476, Bottom := 70.241013}
                    ZOrder := 1000
                    Widget := HitNext

            # Skill Hits
            set Slots += array:
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 365.0, Top := -134.0, Right := 220.0, Bottom := 60.0}
                    ZOrder := 1000
                    Widget := Hit1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 365.0, Top := -4.2, Right := 220.0, Bottom := 60.0}
                    ZOrder := 1000
                    Widget := Hit2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 365.0, Top := 125.6, Right := 220.0, Bottom := 60.0}
                    ZOrder := 1000
                    Widget := Hit3
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := 365.0, Top := 255.4, Right := 220.0, Bottom := 60.0}
                    ZOrder := 1000
                    Widget := Hit4

            # Skill Points Boxes
            set Slots += array:
                # Page 1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -133.398987, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P1_S1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -3.598984, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P1_S2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 129.552063, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P1_S3
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 260.01358, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P1_S4
                # Page 2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -133.398987, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P2_S1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -3.598984, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P2_S2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 129.552063, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P2_S3
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 260.01358, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P2_S4
                # Page 3
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -133.398987, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P3_S1
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := -3.598984, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P3_S2
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 129.552063, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P3_S3
                canvas_slot:
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    SizeToContent := false
                    Offsets := margin{Left := -32.201759, Top := 260.01358, Right := 596.547852, Bottom := 62.802032}
                    ZOrder := 900
                    Widget := Menu.SkillPointsBox_P3_S4

            # TEXT BLOCKS (Counters)
            set Slots += array:
                # Row 1 (Page 1, 2, 3)
                canvas_slot:
                    Widget := Menu.TB_P1_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -188.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P2_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -188.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P3_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -188.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

                # Row 2
                canvas_slot:
                    Widget := Menu.TB_P1_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -58.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P2_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -58.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P3_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := -58.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

                # Row 3
                canvas_slot:
                    Widget := Menu.TB_P1_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 71.6, Right := 80.22, Bottom := 71.6}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P2_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 71.6, Right := 80.22, Bottom := 71.6}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P3_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 71.6, Right := 80.22, Bottom := 71.6}
                    ZOrder := 90

                # Row 4
                canvas_slot:
                    Widget := Menu.TB_P1_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 201.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P2_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 201.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_P3_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 192.0, Top := 201.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

            # TEXT BLOCKS (Costs)
            set Slots += array:
                # Row 1 Cost
                canvas_slot:
                    Widget := Menu.TB_Cost_P1_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -192.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P2_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -192.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P3_S1
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -192.0, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

                # Row 2 Cost
                canvas_slot:
                    Widget := Menu.TB_Cost_P1_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -62.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P2_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -62.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P3_S2
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := -62.2, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

                # Row 3 Cost
                canvas_slot:
                    Widget := Menu.TB_Cost_P1_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 67.6, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P2_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 67.6, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P3_S3
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 67.6, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

                # Row 4 Cost
                canvas_slot:
                    Widget := Menu.TB_Cost_P1_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 197.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P2_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 197.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90
                canvas_slot:
                    Widget := Menu.TB_Cost_P3_S4
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := 416.0, Top := 197.4, Right := 80.22, Bottom := 38.4}
                    ZOrder := 90

            # PLAYER CURRENCY DISPLAY
            set Slots += array:
                canvas_slot:
                    Widget := Menu.TB_PlayerCurrency
                    Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                    Alignment := vector2{X:=0.5, Y:=0.5}
                    Offsets := margin{Left := -261.09, Top := -276.49, Right := 253.0, Bottom := 41.0}
                    ZOrder := 90

            # Paging Icons
            var NoPrevW : ?widget = false
            if(Tex := NoPrevPageIcon?):
                PrevIcon := Menu.MakePagingIcon(Tex)
                set NoPrevW = option{PrevIcon}
                set Slots += array:
                    canvas_slot:
                        Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                        Alignment := vector2{X:=0.5, Y:=0.5}
                        SizeToContent := false
                        Offsets := margin{Left := 520.0, Top := -1.8, Right := 231.0, Bottom := 231.0}
                        ZOrder := 950
                        Widget := PrevIcon

            var NoNextW : ?widget = false
            if(Tex := NoNextPageIcon?):
                NextIcon := Menu.MakePagingIcon(Tex)
                set NoNextW = option{NextIcon}
                set Slots += array:
                    canvas_slot:
                        Anchors := anchors{Minimum := vector2{X:=0.5, Y:=0.5}, Maximum := vector2{X:=0.5, Y:=0.5}}
                        Alignment := vector2{X:=0.5, Y:=0.5}
                        SizeToContent := false
                        Offsets := margin{Left := 520.0, Top := 123.6, Right := 231.0, Bottom := 231.0}
                        ZOrder := 950
                        Widget := NextIcon

            Canvas := canvas:
                Slots := Slots

            PlayerUI.AddWidget(Canvas, player_ui_slot{InputMode := ui_input_mode.All})

            ApplyPageVisibility(Menu, PageIndex, NoPrevW, NoNextW, Hit1, Hit2, Hit3, Hit4)

            Menu.ForceHover(5)
            Menu.StartHoverTransitionLock()
            
            # Start background loop to update currency display
            spawn{Menu.RunCurrencyUpdateLoop()}

            loop:
                var Action : int = 0
                race:
                    block:
                        Menu.ExitEvent.Await()
                        set Action = 3
                    block:
                        Menu.NextPageEvent.Await()
                        set Action = 2
                    block:
                        Menu.PrevPageEvent.Await()
                        set Action = 1

                if(Action = 3):
                    break
                else if(Action = 2):
                    if(PageIndex < 3):
                        Menu.RememberNavHoverOnLeave(PageIndex)
                        Menu.ResetPageAnimVars(PageIndex)
                        set PageIndex += 1
                        set Menu.CurrentPage = PageIndex
                        Menu.ResetHoverArbiter()

                        Menu.ResetPageAnimVars(PageIndex)
                        Menu.StartHoverTransitionLock()
                        ApplyPageVisibility(Menu, PageIndex, NoPrevW, NoNextW, Hit1, Hit2, Hit3, Hit4)
                else:
                    if(PageIndex > 1):
                        Menu.RememberNavHoverOnLeave(PageIndex)
                        Menu.ResetPageAnimVars(PageIndex)
                        set PageIndex -= 1
                        set Menu.CurrentPage = PageIndex
                        Menu.ResetHoverArbiter()

                        Menu.ResetPageAnimVars(PageIndex)
                        Menu.StartHoverTransitionLock()
                        ApplyPageVisibility(Menu, PageIndex, NoPrevW, NoNextW, Hit1, Hit2, Hit3, Hit4)

            Menu.MenuCloseEvent.Signal()
            PlayerUI.RemoveWidget(Canvas)
