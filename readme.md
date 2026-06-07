# ODViz - Open Data Visualization

**ODViz (Open Data Visualization)** is a lightweight, unified format for embedding tabular data and their visual representations within SVG documents. It supports any visualization type including statistical charts, maps, network graphs, trees, flow diagrams, and more. The format is designed to be simple, readable, and easy to process, while maintaining bidirectional references between data and visual elements.

As part of the broader **Open Data Science** ecosystem, ODViz promotes transparency, reproducibility, and interoperability in data analysis. By embedding both data and their visual representations in a single, self-contained SVG file, ODViz enables seamless sharing, publishing, and archiving of analytical results. This aligns with Open Data Science principles of making data-driven insights accessible, verifiable, and reusable across different tools and platforms.

---

## Table of Contents

1. [Philosophy](#philosophy)
2. [Namespace](#namespace)
3. [Core Concepts](#core-concepts)
4. [Data Format](#data-format)
5. [Bidirectional References](#bidirectional-references)
6. [Table Structure](#table-structure)
7. [Optional Metadata](#optional-metadata)
8. [Columns Definition](#columns-definition)
9. [Statistics](#statistics)
10. [Relations Between Tables](#relations-between-tables)
11. [History and Validity](#history-and-validity)
12. [Quality Metadata](#quality-metadata)
13. [Visualization Examples](#visualization-examples)
14. [Complete Example](#complete-example)
15. [XML Schema](#xml-schema)

---

## Philosophy

ODViz follows these principles:

| Principle | Description |
|-----------|-------------|
| **CSV inside XML** | Data rows are pure CSV, not scattered attributes |
| **Unified visualization** | Same format works for charts, maps, graphs, trees, and more |
| **Bidirectional linking** | Rows know their shapes (`hrefs`), shapes know their row (`href`) |
| **Separation of concerns** | Schema (`columns`) separate from data (CSV rows) |
| **Lightweight** | Minimal verbosity, easy to parse |
| **Standard-compliant** | Uses standard SVG elements and attributes |
| **Extensible** | New visualization types can be added without changing the core format |

---

## Namespace

The recommended namespace alias is short for convenience:

```svg
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:v="https://odviz.org/1.0/">
```

| Component | Value |
|-----------|-------|
| **Prefix** | `v` (short alias) |
| **Full name** | `odviz` |
| **Namespace URI** | `https://odviz.org/1.0/` |
| **Version** | 1.0 |

Using a short alias like `v:` keeps the code clean and readable, especially when repeated on many rows and shapes.

---

## Core Concepts

### Key Attributes

| Attribute | Used On | Cardinality | Purpose |
|-----------|---------|-------------|---------|
| `id` | Row and Shape | 1 | Unique identifier |
| `hrefs` | `<v:row>` | 1 or more | List of shape IDs that represent this row |
| `href` | Shape (`<rect>`, `<circle>`, `<text>`, etc.) | 1 | ID of the row this shape represents |

### Row Attributes

| Attribute | Required | Description | Example |
|-----------|----------|-------------|---------|
| `id` | Yes | Unique row identifier | `"sale-0"` |
| `hrefs` | No | Comma-separated list of shape IDs | `"bar-0, label-0, icon-0"` |

### Shape Attributes

| Attribute | Required | Description | Example |
|-----------|----------|-------------|---------|
| `id` | Yes | Unique shape identifier | `"bar-0"` |
| `href` | No (but recommended) | Reference to the data row | `"#sale-0"` |

---

## Data Format

### CSV Inside Rows

Data is stored as pure CSV within each `<v:row>` element. The column names are defined in the `columns` attribute of the table.

```svg
<v:table name="products" columns="id,name,price,stock">
  <v:row id="prod-001" hrefs="rect-001, text-001">
    001,Product A,99.90,150
  </v:row>
  <v:row id="prod-002" hrefs="rect-002, text-002">
    002,Product B,149.50,85
  </v:row>
</v:table>
```

### CSV Rules

- The first line of CSV is never present in rows (headers are in `columns` attribute)
- Values are separated by commas
- Spaces after commas are optional
- All rows must have the same number of values as columns

---

## Bidirectional References

### Row to Shapes (`hrefs`)

The `hrefs` attribute in `<v:row>` lists all shape IDs that represent that row:

```svg
<v:row id="vp-0" hrefs="bar-0, label-0, icon-0, value-0">
  0,Product A,150,#2196F3
</v:row>
```

### Shape to Row (`href`)

Each shape uses `href` to point back to its row:

```svg
<rect id="bar-0" href="#vp-0" x="50" y="100" width="80" height="150" fill="#2196F3"/>
<text id="label-0" href="#vp-0" x="90" y="270">Product A</text>
<circle id="icon-0" href="#vp-0" cx="50" cy="50" r="10"/>
<text id="value-0" href="#vp-0" x="90" y="95">150</text>
```

### Reference Rules

- `hrefs` values must be comma-separated IDs of existing shapes
- `href` values must start with `#` followed by an existing row ID
- A shape can only reference one row
- A row can reference multiple shapes

---

## Table Structure

### Basic Table

```svg
<v:table name="sales_data" columns="id,product,value,color">
  <v:row id="row-0" hrefs="bar-0, label-0">
    0,Product A,150,#2196F3
  </v:row>
  <v:row id="row-1" hrefs="bar-1, label-1">
    1,Product B,85,#4CAF50
  </v:row>
</v:table>
```

### Table Attributes

| Attribute | Required | Description | Example |
|-----------|----------|-------------|---------|
| `name` | Yes | Unique table identifier | `"sales_data"` |
| `columns` | Yes | CSV column names | `"id,product,value"` |
| `description` | No | Human-readable description | `"Sales by product 2024"` |
| `version` | No | Version number | `"1.0"` |
| `visualization` | No | Suggested visualization type | `"chart"`, `"map"`, `"graph"`, `"tree"` |
| `created` | No | Creation timestamp | `"2024-06-06T10:30:00Z"` |
| `updated` | No | Last update timestamp | `"2024-12-15T14:22:00Z"` |
| `source` | No | Data source | `"ERP_SAP"` |
| `author` | No | Creator name | `"Ana López"` |
| `department` | No | Responsible department | `"Sales"` |
| `license` | No | Usage license | `"CC-BY-4.0"` |
| `language` | No | ISO language code | `"en"` |
| `timezone` | No | IANA timezone | `"America/New_York"` |
| `currency` | No | ISO currency code | `"USD"` |
| `unit` | No | Unit of measurement | `"thousands"` |
| `decimalSeparator` | No | Decimal separator | `"."` |
| `thousandSeparator` | No | Thousands separator | `","` |
| `nullValue` | No | Null representation | `"N/A"` |
| `primaryKey` | No | Primary key column | `"id"` |
| `totalRows` | No | Total number of rows | `"150"` |

---

## Optional Metadata

### Complete Table with Metadata

```svg
<v:table name="sales_2024"
         description="Monthly sales by product and region"
         visualization="chart"
         version="1.0"
         created="2024-06-06T10:30:00Z"
         updated="2024-12-15T14:22:00Z"
         source="ERP_SAP"
         author="Ana López"
         department="Sales"
         license="CC-BY-4.0"
         language="en"
         timezone="America/Mexico_City"
         currency="MXN"
         unit="thousands"
         decimalSeparator="."
         thousandSeparator=","
         nullValue="N/A"
         columns="id,product,region,sales,target,percentage"
         primaryKey="id">
  
  <!-- Additional metadata elements go here -->
  
</v:table>
```

---

## Columns Definition

Detailed column definitions provide data type validation and documentation:

```svg
<v:columns>
  <v:column name="id" type="integer" description="Unique identifier" required="true"/>
  <v:column name="product" type="string" description="Product name" required="true"/>
  <v:column name="region" type="string" description="Sales region" enumeration="North,South,East,West"/>
  <v:column name="sales" type="currency" description="Total sales" unit="MXN" min="0"/>
  <v:column name="target" type="currency" description="Sales target" unit="MXN" min="0"/>
  <v:column name="percentage" type="percentage" description="Target achievement" formula="(sales/target)*100" readonly="true"/>
</v:columns>
```

### Column Attributes

| Attribute | Type | Description |
|-----------|------|-------------|
| `name` | string | Column identifier |
| `type` | enum | `string`, `integer`, `decimal`, `date`, `datetime`, `boolean`, `percentage`, `currency` |
| `description` | string | Human-readable description |
| `unit` | string | Unit of measurement |
| `enumeration` | string | Comma-separated list of allowed values |
| `min` | number | Minimum allowed value |
| `max` | number | Maximum allowed value |
| `required` | boolean | Whether column must have a value |
| `formula` | string | Expression for calculated columns |
| `readonly` | boolean | Whether column is read-only |

---

## Statistics

Pre-calculated statistics for better performance:

```svg
<v:statistics>
  <v:total column="sales">1250000</v:total>
  <v:average column="sales">104166.67</v:average>
  <v:min column="sales">85000</v:min>
  <v:max column="sales">280000</v:max>
  
  <v:sum column="sales" groupBy="product">
    <v:group key="Product A">450000</v:group>
    <v:group key="Product B">380000</v:group>
    <v:group key="Product C">420000</v:group>
  </v:sum>
</v:statistics>
```

### Statistics Elements

| Element | Attributes | Description |
|---------|------------|-------------|
| `total` | `column` | Sum of all values in column |
| `average` | `column` | Mean of values in column |
| `min` | `column` | Minimum value in column |
| `max` | `column` | Maximum value in column |
| `sum` | `column`, `groupBy` | Aggregated sum, optionally grouped |

---

## Relations Between Tables

Define foreign key relationships between tables:

```svg
<v:relations>
  <v:relation target="products" localKey="product_id" foreignKey="id" type="inner"/>
  <v:relation target="regions" localKey="region_code" foreignKey="code" type="left"/>
  <v:relation target="time_dimension" localKey="date" foreignKey="date" type="inner"/>
</v:relations>
```

### Relation Attributes

| Attribute | Description | Values |
|-----------|-------------|--------|
| `target` | Name of the referenced table | Table name string |
| `localKey` | Column name in current table | Column identifier |
| `foreignKey` | Column name in target table | Column identifier |
| `type` | Join type | `inner`, `left`, `right`, `full` (default: `inner`) |

---

## History and Validity

Track changes and temporal validity:

```svg
<v:history>
  <v:change date="2024-06-06T10:30:00Z" user="ana.lopez">
    Initial creation
  </v:change>
  <v:change date="2024-07-01T09:15:00Z" user="carlos.ruiz">
    Added percentage column
  </v:change>
</v:history>

<v:validity>
  <v:validFrom>2024-01-01</v:validFrom>
  <v:validTo>2024-12-31</v:validTo>
  <v:refreshInterval>P1D</v:refreshInterval>
</v:validity>
```

### Validity Elements

| Element | Format | Description |
|---------|--------|-------------|
| `validFrom` | ISO date | Start of validity period |
| `validTo` | ISO date | End of validity period |
| `refreshInterval` | ISO duration | Recommended refresh frequency |

---

## Quality Metadata

Data quality indicators:

```svg
<v:quality>
  <v:completeness>0.98</v:completeness>
  <v:accuracy>high</v:accuracy>
  <v:lastValidation>2024-12-15T08:00:00Z</v:lastValidation>
</v:quality>
```

### Quality Values

| Element | Description | Values |
|---------|-------------|--------|
| `completeness` | Fraction of non-null values | 0.0 to 1.0 |
| `accuracy` | Data accuracy level | `low`, `medium`, `high` |
| `lastValidation` | Last validation timestamp | ISO 8601 datetime |

---

## Visualization Examples

### 1. Bar Chart

```svg
<v:table name="sales" columns="id,product,value,color" visualization="chart">
  <v:row id="sale-0" hrefs="bar-0, label-0">0,Product A,150,#2196F3</v:row>
  <v:row id="sale-1" hrefs="bar-1, label-1">1,Product B,85,#4CAF50</v:row>
  <v:row id="sale-2" hrefs="bar-2, label-2">2,Product C,234,#FF9800</v:row>
</v:table>

...

<rect id="bar-0" href="#sale-0" x="50" y="100" width="80" height="150" fill="#2196F3"/>
<text id="label-0" href="#sale-0" x="90" y="270">Product A</text>

<rect id="bar-1" href="#sale-1" x="160" y="170" width="80" height="85" fill="#4CAF50"/>
<text id="label-1" href="#sale-1" x="200" y="270">Product B</text>

<rect id="bar-2" href="#sale-2" x="270" y="50" width="80" height="234" fill="#FF9800"/>
<text id="label-2" href="#sale-2" x="310" y="270">Product C</text>
```

### 2. Choropleth Map

```svg
<v:table name="regions" columns="id,name,value,color" visualization="map">
  <v:row id="region-north" hrefs="path-north">north,North Region,45,#2196F3</v:row>
  <v:row id="region-south" hrefs="path-south">south,South Region,32,#4CAF50</v:row>
  <v:row id="region-east" hrefs="path-east">east,East Region,15,#FF9800</v:row>
</v:table>

...

<path id="path-north" href="#region-north" d="M10,10 L100,10 L100,100 L10,100 Z" fill="#2196F3"/>
<path id="path-south" href="#region-south" d="M10,110 L100,110 L100,200 L10,200 Z" fill="#4CAF50"/>
<path id="path-east" href="#region-east" d="M110,10 L200,10 L200,100 L110,100 Z" fill="#FF9800"/>
```

### 3. Network Graph

A network graph consists of two related tables: one for nodes and one for edges. The `relations` metadata defines how they connect.

```svg
<!-- Nodes table -->
<v:table name="network_nodes" columns="id,label,value,color" visualization="graph">
  <v:row id="node-0" hrefs="circle-0, text-0">0,Node A,10,#2196F3</v:row>
  <v:row id="node-1" hrefs="circle-1, text-1">1,Node B,15,#4CAF50</v:row>
  <v:row id="node-2" hrefs="circle-2, text-2">2,Node C,8,#FF9800</v:row>
</v:table>

<!-- Edges table with relation to nodes -->
<v:table name="network_edges" columns="id,source,target,weight">
  
  <v:relations>
    <v:relation target="network_nodes" localKey="source" foreignKey="id"/>
    <v:relation target="network_nodes" localKey="target" foreignKey="id"/>
  </v:relations>
  
  <v:row id="edge-0" hrefs="line-0">0,0,1,5</v:row>
  <v:row id="edge-1" hrefs="line-1">1,0,2,3</v:row>
</v:table>

...

<!-- Node shapes -->
<circle id="circle-0" href="#node-0" cx="100" cy="100" r="20" fill="#2196F3"/>
<text id="text-0" href="#node-0" x="100" y="105">Node A</text>

<circle id="circle-1" href="#node-1" cx="300" cy="200" r="25" fill="#4CAF50"/>
<text id="text-1" href="#node-1" x="300" y="205">Node B</text>

<circle id="circle-2" href="#node-2" cx="200" cy="300" r="15" fill="#FF9800"/>
<text id="text-2" href="#node-2" x="200" y="305">Node C</text>

<!-- Edge shapes -->
<line id="line-0" href="#edge-0" x1="100" y1="100" x2="300" y2="200" stroke="#999" stroke-width="2"/>
<line id="line-1" href="#edge-1" x1="100" y1="100" x2="200" y2="300" stroke="#999" stroke-width="2"/>
```

### 4. Radial Tree

```svg
<v:table name="hierarchy" columns="id,parent_id,name,size" visualization="tree">
  <v:row id="node-0" hrefs="circle-0, text-0">0,null,Root,50</v:row>
  <v:row id="node-1" hrefs="circle-1, text-1, path-1">1,0,Child A,30</v:row>
  <v:row id="node-2" hrefs="circle-2, text-2, path-2">2,0,Child B,25</v:row>
</v:table>

... 

<circle id="circle-0" href="#node-0" cx="400" cy="300" r="30" fill="#2196F3"/>
<text id="text-0" href="#node-0" x="400" y="305">Root</text>

<circle id="circle-1" href="#node-1" cx="300" cy="200" r="20" fill="#4CAF50"/>
<text id="text-1" href="#node-1" x="300" y="205">Child A</text>
<path id="path-1" href="#node-1" d="M400,300 L300,200" stroke="#999" stroke-width="2"/>

<circle id="circle-2" href="#node-2" cx="500" cy="200" r="18" fill="#FF9800"/>
<text id="text-2" href="#node-2" x="500" y="205">Child B</text>
<path id="path-2" href="#node-2" d="M400,300 L500,200" stroke="#999" stroke-width="2"/>
```

### 5. Pie Chart

```svg
<v:table name="pie_data" columns="id,category,value,color,percentage" visualization="chart">
  <v:row id="slice-0" hrefs="path-0, label-0">0,Category A,30,#2196F3,30</v:row>
  <v:row id="slice-1" hrefs="path-1, label-1">1,Category B,45,#4CAF50,45</v:row>
  <v:row id="slice-2" hrefs="path-2, label-2">2,Category C,25,#FF9800,25</v:row>
</v:table>

...

<path id="path-0" href="#slice-0" d="M200,200 L200,80 A120,120 0 0,1 306,127 Z" fill="#2196F3"/>
<path id="path-1" href="#slice-1" d="M200,200 L306,127 A120,120 0 0,1 156,306 Z" fill="#4CAF50"/>
<path id="path-2" href="#slice-2" d="M200,200 L156,306 A120,120 0 0,1 200,80 Z" fill="#FF9800"/>

<text id="label-0" href="#slice-0" x="260" y="100">Category A (30%)</text>
<text id="label-1" href="#slice-1" x="310" y="220">Category B (45%)</text>
<text id="label-2" href="#slice-2" x="150" y="310">Category C (25%)</text>
```

### 6. Scatter Plot

```svg
<v:table name="scatter_data" columns="id,x,y,value,color" visualization="chart">
  <v:row id="point-0" hrefs="circle-0">0,10,20,150,#2196F3</v:row>
  <v:row id="point-1" hrefs="circle-1">1,30,25,85,#4CAF50</v:row>
  <v:row id="point-2" hrefs="circle-2">2,50,15,234,#FF9800</v:row>
  <v:row id="point-3" hrefs="circle-3">3,70,45,30,#9C27B0</v:row>
</v:table>

...

<circle id="circle-0" href="#point-0" cx="100" cy="200" r="10" fill="#2196F3"/>
<circle id="circle-1" href="#point-1" cx="200" cy="180" r="8" fill="#4CAF50"/>
<circle id="circle-2" href="#point-2" cx="300" cy="220" r="12" fill="#FF9800"/>
<circle id="circle-3" href="#point-3" cx="400" cy="100" r="6" fill="#9C27B0"/>
```

---

## Complete Example

```svg
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:xlink="http://www.w3.org/1999/xlink"
     xmlns:v="https://odviz.org/1.0/"
     width="800" height="600" viewBox="0 0 800 600">
  
  <metadata>
    
    <!-- Products dimension table -->
    <v:table name="products" columns="id,name,color">
      <v:row id="prod-001">prod-001,Product A,#2196F3</v:row>
      <v:row id="prod-002">prod-002,Product B,#4CAF50</v:row>
      <v:row id="prod-003">prod-003,Product C,#FF9800</v:row>
    </v:table>
    
    <!-- Sales fact table -->
    <v:table name="sales"
             description="Sales by product 2024"
             visualization="chart"
             version="1.0"
             created="2024-06-06T10:30:00Z"
             author="Ana López"
             currency="USD"
             columns="id,product_id,value"
             primaryKey="id">
      
      <v:relations>
        <v:relation target="products" localKey="product_id" foreignKey="id"/>
      </v:relations>
      
      <v:statistics>
        <v:total column="value">469</v:total>
        <v:average column="value">156.33</v:average>
      </v:statistics>
      
      <v:row id="sale-0" hrefs="bar-0, label-0, value-0">
        0,prod-001,150
      </v:row>
      <v:row id="sale-1" hrefs="bar-1, label-1, value-1">
        1,prod-002,85
      </v:row>
      <v:row id="sale-2" hrefs="bar-2, label-2, value-2">
        2,prod-003,234
      </v:row>
      
      <v:quality>
        <v:completeness>1.0</v:completeness>
        <v:accuracy>high</v:accuracy>
      </v:quality>
      
    </v:table>
    
  </metadata>
  
  <!-- Visual shapes -->
  <g transform="translate(50, 50)">
    
    <rect id="bar-0" href="#sale-0" x="50" y="120" width="80" height="150" fill="#2196F3"/>
    <text id="label-0" href="#sale-0" x="90" y="290" text-anchor="middle">Product A</text>
    <text id="value-0" href="#sale-0" x="90" y="115" text-anchor="middle">150</text>
    
    <rect id="bar-1" href="#sale-1" x="160" y="170" width="80" height="85" fill="#4CAF50"/>
    <text id="label-1" href="#sale-1" x="200" y="290" text-anchor="middle">Product B</text>
    <text id="value-1" href="#sale-1" x="200" y="165" text-anchor="middle">85</text>
    
    <rect id="bar-2" href="#sale-2" x="270" y="50" width="80" height="234" fill="#FF9800"/>
    <text id="label-2" href="#sale-2" x="310" y="290" text-anchor="middle">Product C</text>
    <text id="value-2" href="#sale-2" x="310" y="45" text-anchor="middle">234</text>
    
  </g>
  
</svg>
```

---

## XML Schema

The official XML Schema for ODViz is available at:

```
https://odviz.org/1.0/odviz.xsd
```

### Schema Location Declaration

```svg
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:v="https://odviz.org/1.0/"
     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="https://odviz.org/1.0/ https://odviz.org/1.0/odviz.xsd">
```

---

## License

ODViz specification is available under the MIT License.

---

## References

- SVG 2 Specification: https://www.w3.org/TR/SVG2/
- XML Namespaces: https://www.w3.org/TR/xml-names/
- CSV on the Web: https://www.w3.org/TR/tabular-data-model/