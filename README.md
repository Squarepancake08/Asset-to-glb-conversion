# Asset to GLB Conversion System

A comprehensive Python-based conversion system that transforms Unity asset files (`.asset`, `.mat`) along with texture maps into optimized glTF 2.0 (`.glb`) files with embedded shading presets.

## Features

- **Unity Asset Parsing**: Extracts mesh data from Unity `.asset` files
- **Material Processing**: Parses `.mat` (YAML) material files with shader parameters
- **Texture Support**: Handles base maps, normal maps, specularity maps, and metallic maps
- **PBR Workflow**: Converts materials to PBR (Physically-Based Rendering) for universal compatibility
- **Embedded Textures**: All textures embedded directly in the GLB file
- **Shading Presets**: Multiple shading models:
  - Specular/Gloss workflow (converts from Unity's SpecGlossMap)
  - Metallic/Roughness workflow (PBR standard)
  - Normal mapping with configurable strength
  - Occlusion mapping

## Installation

```bash
git clone https://github.com/Squarepancake08/Asset-to-glb-conversion.git
cd Asset-to-glb-conversion
pip install -r requirements.txt
```

## Requirements

- Python 3.8+
- PyYAML: For parsing Unity YAML files
- Pillow: For image processing
- numpy: For numerical operations
- pygltflib: For GLB/glTF creation

## Usage

### Basic Conversion

```python
from converter import AssetConverter

converter = AssetConverter()
converter.convert(
    mesh_asset='path/to/DresserFrame.asset',
    material_file='path/to/DormRoom_BunkBedDresser_Mat.mat',
    texture_dir='path/to/textures/',
    output_glb='output.glb'
)
```

### Command Line Usage

```bash
python main.py --mesh DresserFrame.asset \
               --material DormRoom_BunkBedDresser_Mat.mat \
               --textures ./textures/ \
               --output output.glb \
               --shading-preset pbr
```

## Project Structure

```
Asset-to-glb-conversion/
├── README.md
├── requirements.txt
├── main.py                    # CLI entry point
├── src/
│   ├── __init__.py
│   ├── converters/
│   │   ├── __init__.py
│   │   ├── asset_parser.py   # Unity .asset mesh parser
│   │   ├── material_parser.py # Unity .mat material parser
│   │   └── glb_exporter.py   # GLB/glTF 2.0 exporter
│   ├── texture/
│   │   ├── __init__.py
│   │   └── processor.py       # Texture processing & conversion
│   ├── shading/
│   │   ├── __init__.py
│   │   └── materials.py       # PBR material definitions
│   └── utils/
│       ├── __init__.py
│       └── helpers.py         # Utility functions
└── examples/
    └── example_conversion.py
```

## Supported Formats

### Input
- `.asset` - Unity mesh files
- `.mat` - Unity material files (YAML format)
- `.png`, `.jpg`, `.tga` - Texture maps
- Texture Types:
  - Base/Albedo maps (`_BaseMap`, `_MainTex`)
  - Normal maps (`_BumpMap`, `_NormalMap`)
  - Specularity maps (`_SpecGlossMap`)
  - Metallic maps (`_MetallicGlossMap`)
  - Occlusion maps (`_OcclusionMap`)

### Output
- `.glb` - Binary glTF 2.0 with embedded textures

## Shading Presets

### Specular/Gloss (Unity Legacy)
- Uses SpecGlossMap for specularity and glossiness
- Best for older Unity Standard shader assets

### Metallic/Roughness (PBR Standard)
- Converts from specularity to metallic/roughness workflow
- Universal compatibility with modern engines
- Default for best compatibility

### Custom
- Define custom material properties via JSON config

## Example Material Conversion

**Input (Unity .mat):**
```yaml
Material:
  m_Name: DormRoom_BunkBedDresser_Mat
  m_Shader: {fileID: 46}  # Standard PBR shader
  m_SavedProperties:
    m_TexEnvs:
      - _BaseMap: {guid: 985cc739729f05e4eb6aaae638e9434a}
      - _BumpMap: {guid: 306c0365f359881489a9e5bf4de799bb}
      - _SpecGlossMap: {guid: 161164d19afc0744bae207d64ffa8663}
    m_Floats:
      - _BumpScale: 0.64
      - _Smoothness: 0.5
```

**Output (GLB):**
- Embedded base texture
- Embedded normal map (with 0.64 scale)
- Converted specularity → metallic/roughness maps
- Material with proper glTF extensions

## Architecture

### Asset Parser
Extracts binary mesh data from Unity `.asset` files:
- Vertex positions, normals, tangents
- UV coordinates
- Index buffers

### Material Parser
Reads Unity YAML material files:
- Shader type identification
- Texture references (GUIDs → file paths)
- Numeric parameters (scale, smoothness, metallic)
- Color values

### Texture Processor
- Loads textures from referenced GUIDs or paths
- Converts between color spaces (Linear ↔ sRGB)
- Generates PBR maps from Unity-specific formats
- Optimizes resolution while maintaining quality

### GLB Exporter
Creates glTF 2.0 compliant files:
- Embeds all geometry and textures
- Applies proper material definitions
- Includes metadata (author, copyright, etc.)
- Validates output for compatibility

## Known Limitations

- Requires asset GUIDs to be resolved to actual file paths
- Rigged/skinned meshes: basic support (bones exported as static)
- Animation data: not currently supported
- Some advanced shader features may not transfer perfectly

## Performance

- Typical conversion: < 5 seconds for high-poly assets
- File size: Varies with texture resolution (256x256 → ~2MB, 2048x2048 → ~32MB)

## Contributing

Contributions welcome! Please follow PEP 8 style guide and add tests for new features.

## License

MIT License - See LICENSE file for details

## Support & Issues

For bug reports and feature requests, please open an issue on GitHub.
