# **DropdownComponent**

## **Description**

Renders an options selector as a dropdown , used in many other GreenFire components.

## **Properties**

### isDropdownOpen

Defines if the dropdown is open and visually present.

### setIsDropdownOpen

Opens or closes the dropdown.

### withTopPadding

Signals the dropdown to apply top padding. Since the dropdown options support a header element, this property allows to switch from padding to no padding to see what fits better.

### selectedColor

Color applied to a dropdown option if it is marked as selected, sent as rgb in the format of xxx xxx xxx (no comma between values).

### elements

The elements that the dropdown contain. This are structure in the next way:

```csharp
type DropdownElement = | DropdownItem | DropdownHeader;

interface DropdownHeader {
    type: DropdownElementType.Header;
    title: string;
    subtitle?: string;
}

interface DropdownItem {
    type: DropdownElementType.Item;
    icon?: FunctionComponent<SVGProps<SVGSVGElement>>;
    iconSize?: number;
    color?: string;
    selected?: boolean;
    text: string;
    onClick?: () => void;
}

enum DropdownElementType{
    Header,
    Item
}
```

## **DropdownHeader**

A simple header meant to rely information related to the context of the popup.

### title

The title of the header.

### subtitle

The subtitle of the header.

## **DropdownItem**

A simple header meant to rely information related to the context of the popup.

### icon

Icon to be shown at the left of the text. Any SVG image is supported, included Lucide Icons.

### iconSize

The size of the icon. By default is 20, which means a 20 width and height.

### color

Color for both the text and icon, sent as rgb in the format of xxx xxx xxx (no comma between values).

### selected

Defines if the element is selected. For example, it is used for dropdowns where clicking the option will define a selection, like a theme toggle dropdown.

### text

The text shown inside the element.

### onClick

Function executed when the element is clicked.

## Example

In this example, a Language button component is created, where by clicking one of the two language options, the **language** property from the **useLocalization** hook will be set, and that option element marked as selected. The FlagEs and FlagUs are SVG icons, while Languages is a Lucide Icon.

=== "ASP.NET"

```csharp
function LanguageButton({ isLanguageMenuOpen, setIsLanguageMenuOpen, iconColor }: LanguageButtonProps) {
    const { language, translate, setLanguage } = useLocalization();

    const getLanguageDropdownElements = () => {
        const elements: DropdownItem[] = [
            {
                type: DropdownElementType.Item,
                text: "Spanish",
                icon: FlagEs,
                selected: language == "es",
                onClick: () => setLanguage("es")
            },
            {
                type: DropdownElementType.Item,
                text: "English",
                icon: FlagUs,
                selected: language == "en",
                onClick: () => setLanguage("en")
            }
        ];

        return elements;
    }

    return <div className="language-wrapper" onClick={() => setIsLanguageMenuOpen(!isLanguageMenuOpen)}>
        <button className={`language-button ${isLanguageMenuOpen ? "menu-open" : ""} `}>
            <Languages className="language-button-img" style={{color: iconColor ?? ""}} />
        </button>
        {isLanguageMenuOpen && (
            <DropdownComponent
                isDropdownOpen={isLanguageMenuOpen}
                setIsDropdownOpen={setIsLanguageMenuOpen}
                withTopPadding={true}
                selectedColor="--color-primary-blue-hover"
                elements={getLanguageDropdownElements()}
            />
        )}
    </div>
}
```
