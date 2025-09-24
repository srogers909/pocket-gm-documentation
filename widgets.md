# Widget Documentation

This document provides a comprehensive overview of all widgets in the Pro Football Clone project, organized alphabetically.

## UI Widgets (`ui/lib/widgets/`)

### DashboardCard
**File:** `ui/lib/widgets/dashboard_card.dart`

**Purpose:** A reusable boxed dashboard card with consistent border and margin styling from ThemeData.cardTheme. Features an expandable/collapsible tile with a title header.

**Key Features:**
- Expandable/collapsible content with title header
- Configurable initial expansion state
- Customizable content padding
- Consistent theming through Material Design

**Localization Status:** ✅ Clean - No hardcoded text (title passed as parameter)

---

### GenerationSourceModal
**File:** `ui/lib/widgets/generation_source_modal.dart`

**Purpose:** Modal dialog that prompts users to choose between loading data from online sources or generating new league data locally.

**Key Features:**
- Two-button choice interface (Load from Online vs Generate)
- Integrated with league generation system
- Automatically shows team selection modal after generation
- Consistent theming with app colors

**Localization Status:** ✅ Fully localized - Uses AppLocalizations for all text

---

### MainAppBar
**File:** `ui/lib/widgets/main_app_bar.dart`

**Purpose:** Custom application bar with menu and settings buttons, implementing PreferredSizeWidget for AppBar replacement.

**Key Features:**
- Menu button on the left
- Settings button on the right
- Optional title display with overflow handling
- Consistent theming with app colors

**Localization Status:** ⚠️ Needs localization - Hardcoded tooltips:
- `'Menu'` - Menu button tooltip
- `'Settings'` - Settings button tooltip

---

### PlayerListItem
**File:** `ui/lib/widgets/player_list_item.dart`

**Purpose:** Complex expandable card widget for displaying detailed player information including ratings, physical attributes, and position-specific data.

**Key Features:**
- Expandable player card with rating badge
- Detailed player information in expansion panel
- Visual rating system with color-coded grades
- Physical attributes display with flag icons
- Position-specific ratings with progress bars
- Fan nickname display

**Localization Status:** ❌ Extensive localization needed - Many hardcoded strings:
- `'Height: ${player.heightFormatted}'`
- `'Weight: ${player.weightLbs} lbs'`
- `'College: ${player.college}'`
- `'Born: ${player.birthInfo}'`
- `'Nickname: "${player.fanNickname}"'`
- `'Physical Attributes'`
- `'Core Ratings'`
- `'Position Ratings (${player.primaryPosition})'`
- `'Overall'`, `'Potential'`, `'Durability'`, `'Football IQ'`, `'Fan Popularity'`, `'Morale'`
- Grade labels: `'F'`, `'D'`, `'C'`, `'B'`, `'A'`, `'A+'`

---

### SaveSlotModal
**File:** `ui/lib/widgets/save_slot_modal.dart`

**Purpose:** Modal dialog for selecting save slots when creating new games, displaying available empty save slots.

**Key Features:**
- Dynamic save slot button generation (1-3 slots)
- Integration with generation source modal
- Cancel functionality
- Consistent theming

**Localization Status:** ✅ Fully localized - Uses AppLocalizations for all text

---

### TeamSelectionModal
**File:** `ui/lib/widgets/team_selection_modal.dart`

**Purpose:** Modal dialog for selecting a team from generated league data, organized by conferences and divisions with optional rating display.

**Key Features:**
- Conference and division organization
- Team rating toggle display
- Team confirmation dialog
- Rating key with color indicators
- Sorted team display by name
- Integration with team and navigation providers

**Localization Status:** ⚠️ Partial localization - Some strings localized, others hardcoded:

**Localized:**
- Uses `localizations.chooseYourTeam`
- Uses `localizations.ratingKey`

**Needs localization:**
- `'Are you sure you want to control ${team.name}?'` - Confirmation dialog title
- `'Yes!'` - Confirmation button
- `'No'` - Cancel button
- `'Show overall rating'` - Toggle label
- Grade range descriptions: `'A+ (97-100)'`, `'A (93-96)'`, etc.

---

## Pocket GM Widgets (`pocket_gm_widgets/lib/src/`)

### DataGrid
**File:** `pocket_gm_widgets/lib/src/data_grid/data_grid.dart`

**Purpose:** Customizable data grid component with sorting, scrolling, and theming capabilities for displaying tabular data.

**Key Features:**
- Horizontal and vertical scrolling
- Configurable columns with flexible layout
- Sortable columns with visual indicators
- Zebra striping for row legibility
- Row dividers and outer borders
- Empty state placeholder
- Row wrapper functionality
- Responsive column sizing

**Localization Status:** ⚠️ Needs localization - Hardcoded empty state:
- `'No data'` - Empty placeholder message

---

### DataGridColumn
**File:** `pocket_gm_widgets/lib/src/data_grid/data_grid_column.dart`

**Purpose:** Data structure class defining column configuration for DataGrid component.

**Key Features:**
- Header text or custom header builder
- Cell content builder function
- Sorting configuration with custom comparators
- Flexible or fixed width options
- Alignment and styling options
- Tooltip support

**Localization Status:** ✅ Clean - Pure data structure, no user-facing text

---

## Localization Summary

### Files Ready for Production
- ✅ `DashboardCard` - No hardcoded text
- ✅ `GenerationSourceModal` - Fully localized
- ✅ `SaveSlotModal` - Fully localized
- ✅ `DataGridColumn` - Pure data structure

### Files Needing Minor Localization
- ⚠️ `MainAppBar` - 2 tooltip strings
- ⚠️ `DataGrid` - 1 empty state message

### Files Needing Major Localization
- ❌ `PlayerListItem` - 15+ hardcoded strings
- ⚠️ `TeamSelectionModal` - 5+ hardcoded strings

## Recommended Localization Keys

For files needing localization work, here are suggested key additions to `app_en.arb`:

```json
{
  // MainAppBar
  "menuTooltip": "Menu",
  "settingsTooltip": "Settings",
  
  // DataGrid
  "noData": "No data",
  
  // PlayerListItem
  "height": "Height",
  "weight": "Weight",
  "college": "College",
  "born": "Born",
  "nickname": "Nickname",
  "physicalAttributes": "Physical Attributes",
  "coreRatings": "Core Ratings",
  "positionRatings": "Position Ratings ({position})",
  "overall": "Overall",
  "potential": "Potential",
  "durability": "Durability",
  "footballIQ": "Football IQ",
  "fanPopularity": "Fan Popularity",
  "morale": "Morale",
  "gradeF": "F",
  "gradeD": "D",
  "gradeC": "C",
  "gradeB": "B",
  "gradeA": "A",
  "gradeAPlus": "A+",
  
  // TeamSelectionModal
  "confirmTeamSelection": "Are you sure you want to control {teamName}?",
  "yes": "Yes!",
  "no": "No",
  "showOverallRating": "Show overall rating",
  "gradeRangeAPlus": "A+ (97-100)",
  "gradeRangeA": "A (93-96)",
  "gradeRangeAMinus": "A- (90-92)",
  "gradeRangeBPlus": "B+ (87-89)",
  "gradeRangeB": "B (83-86)",
  "gradeRangeBMinus": "B- (80-82)",
  "gradeRangeCPlus": "C+ (77-79)",
  "gradeRangeC": "C (73-76)",
  "gradeRangeCMinus": "C- (70-72)",
  "gradeRangeDPlus": "D+ (67-69)",
  "gradeRangeD": "D (63-66)",
  "gradeRangeDMinus": "D- (60-62)",
  "gradeRangeF": "F (0-59)"
}
```

## Widget Architecture Notes

### State Management
- Several widgets integrate with Provider for state management (`TeamSelectionModal`)
- Most widgets are stateless for simplicity and reusability
- Complex widgets like `PlayerListItem` use local state for expansion

### Theming
- All widgets consistently use `AppColors` and Material Design theming
- Color schemes follow the established app design patterns
- Responsive design considerations for different screen sizes

### Data Flow
- Widgets properly separate data models from presentation
- Generic typing used where appropriate (`DataGrid<T>`, `DataGridColumn<T>`)
- Clean separation between UI widgets and business logic

This documentation should be updated as new widgets are added or existing widgets are modified.
