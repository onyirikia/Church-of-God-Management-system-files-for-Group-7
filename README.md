# Kampala Church of God – Management System (Group 7)

**Course:** 1301 ST – Object Oriented Programming  **Lecturer:** Kinyonyi David Hope
**Client:** Kampala Church of God, Kansanga – Kampala

A modular, menu-driven Java command-line application that replaces the church's
paper books, Excel files and WhatsApp lists with one validated, file-backed system.

## Requirements
* JDK 11 or newer (`javac -version`). No external libraries.

## Build, run, test
| Task  | Linux / macOS | Windows    |
|-------|---------------|------------|
| Build | `./build.sh`  | `build.bat`|
| Run   | `./run.sh`    | `run.bat`  |
| Test  | `./test.sh`   | `test.bat` |

Manual commands (from the project root):

    mkdir bin
    javac -d bin $(find src test -name '*.java')      # Windows: see build.bat
    java -cp bin ug.ac.vu.g07.app.Main
    java -cp bin ug.ac.vu.g07.test.TestRunner

Data is stored in the `data/` folder (created automatically if missing) and loaded at start-up.
The folder ships with a little **sample data** (invented people); delete the `data/*.txt` files for a fresh, empty system.

## Package structure
| Package | Contents |
|---|---|
| `ug.ac.vu.g07.core` | `ChurchRecord` (abstract), `Reportable`, `InputHelper`, `FileStorage`, `IdGenerator`, `RecordNotFoundException` |
| `ug.ac.vu.g07.members` | `Member`, `MembershipType`, `MemberService`, `MemberAlreadyExistsException` |
| `ug.ac.vu.g07.contributions` | `Contribution`, `ContributionType`, `ContributionService`, `InvalidContributionException` |
| `ug.ac.vu.g07.events` | `Event`, `EventAttendance`, `EventService`, `EventFullException` |
| `ug.ac.vu.g07.assets` | `Asset`, `AssetCategory`, `AssetCondition`, `AssetIssue`, `AssetService`, `AssetNotAvailableException` |
| `ug.ac.vu.g07.welfare` | `WelfareCase`, `WelfareSupport`, `CaseStatus`, `WelfareService`, `WelfareLimitExceededException` |
| `ug.ac.vu.g07.app` | `Main` (entry point / main menu) and one console menu class per module |
| `ug.ac.vu.g07.test` (in `test/`) | `TestRunner` – automated test plan |

## Menu
    1 Members      : Add | List (A-Z by name) | Find / Update / Delete
    2 Contributions: Add | Total-per-member report (high -> low) | List all
    3 Events       : Create | Register attendance | Attendance report (by date)
    4 Assets       : Add | Issue / Return | Damaged-assets report | List all | Update condition
    5 Welfare      : Create case | Add support | Open-cases report (oldest first) | Close case
    6 Exit

## Business rules
* IDs: `G07-M001` (member), `G07-C001` (contribution), `G07-E001` (event), `G07-A001` (asset), `G07-W001` (welfare case).
  Press Enter at "Member ID" to auto-generate the next one. Asset issues are numbered `ISS-001`.
* Member: age 0–120, Ugandan phone (`0772123456`, `+256772123456`), join date not in the future; duplicate ID or same name + phone is refused.
* Contribution: linked to an existing member; amount must be > 0 (`InvalidContributionException`).
* Event: capacity enforced (`EventFullException`); a member cannot register twice.
* Asset: cannot issue more than is available or a DAMAGED asset (`AssetNotAvailableException`); returning twice changes nothing.
* Welfare: case linked to an existing member; maximum 3 supports per member per calendar year (`WelfareLimitExceededException`); closed cases take no more support.
* A member that still has contributions, event registrations or welfare cases cannot be deleted.
* Input never crashes the program: every prompt re-asks until the value is valid; no stack traces are shown.

## Object-oriented design
* **Inheritance / abstraction** – `Member`, `Contribution`, `Event`, `Asset`, `WelfareCase` extend abstract `ChurchRecord` (id, created date, abstract `describe()`).
* **Interface** – everything printable implements `Reportable.getReportLine()`; the report loop (`Menu.printReport`) works on `List<Reportable>` (polymorphism).
* **Encapsulation** – private fields, validating setters/constructors throw `IllegalArgumentException` or a module-specific exception.
* **Associations** – `Contribution → Member`, `EventAttendance → Event + Member`, `WelfareSupport → WelfareCase → Member`.
* **Comparators** – members by name, events by date, welfare cases by date, contribution totals high-to-low.

## Data files (`data/`)
`members.txt, contributions.txt, events.txt, attendance.txt, assets.txt, asset_issues.txt, welfare_cases.txt, welfare_supports.txt`
Pipe-delimited text. A missing folder or file starts empty; a corrupted line is skipped with a warning.

## Team (Group 7)
Kirenzi Christopher (Members, Team Leader) · Oyuru Denis (Contributions) · Nyola Fredric William (Events) ·
Kaweesa Denis (Assets) · Buwanguzi Timothy (Welfare Support) · Kenneth Aleku (Attendance / Documentation) · Aloikin Timothy (System Integration)
