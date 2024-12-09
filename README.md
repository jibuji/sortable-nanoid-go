# Sortable NanoID (Go Implementation)

A customizable, time-based, sortable ID generator that combines timestamps with chronological and random parts. Perfect for distributed systems where chronological ordering and high generation rates are important.

## Features

- ⚡ **Time-based Sorting**: Generated IDs are naturally sortable by creation time
- 🔧 **Highly Configurable**: Customize alphabet, length, timestamp precision, and generation rate
- 🌍 **Time Range Control**: Set start/end dates to optimize ID length for your needs
- 🔄 **Stable High-Rate Generation**: Guarantees sortable IDs even at high generation rates
- 🔒 **Secure**: Uses cryptographically secure random number generation
- 📊 **Decodable**: Extract timestamp and chronological information from generated IDs
- 🔄 **Thread-Safe**: Safe for concurrent use

## Installation

```bash
go get github.com/jibuji/sortable-nanoid-go
```

## Quick Start

```go
package main

import (
    "fmt"
    sortablenano "github.com/jibuji/sortable-nanoid-go"
)

func main() {
    // Create a generator with default settings
    generator, err := sortablenano.New(sortablenano.DefaultConfig())
    if err != nil {
        panic(err)
    }

    // Generate a sortable ID
    id, err := generator.Generate()
    if err != nil {
        panic(err)
    }
    fmt.Printf("Generated ID: %s\n", id)

    // Decode the timestamp from an ID
    timestamp, chronoPart, randomPart, err := generator.Decode(id)
    if err != nil {
        panic(err)
    }
    fmt.Printf("Timestamp: %v\nChrono Part: %s\nRandom Part: %s\n", 
        timestamp, chronoPart, randomPart)
}
```

## Default Configuration

The default configuration is designed for general use cases:

```go
DefaultConfig() Config {
    return Config{
        // Base62 alphabet plus "-" and "_" for URL-safe IDs
        Alphabet: "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ-_",
        
        // 32 characters total length, providing good balance between uniqueness and usability
        TotalLength: 32,
        
        // Start from 2024-01-01, adjust this based on your application's needs
        TimestampStart: time.Date(2024, 1, 1, 0, 0, 0, 0, time.UTC),
        
        // Microsecond precision for high-resolution timestamps
        TimestampLevel: Microsecond,
        
        // Support up to 100 IDs per microsecond
        MaxSortableRate: Micro100,
    }
}
```

The default configuration provides:
- **URL-safe IDs**: The alphabet includes only URL-safe characters
- **High Precision**: Microsecond-level timestamp precision
- **High Generation Rate**: Can generate up to 10 unique, sortable IDs per nanosecond
- **Sufficient Length**: 32 characters allow for timestamp, chronological ordering, and randomness
- **Future-Ready**: Starting from 2024, with built-in support for 200 years

## Configuration Options

### Basic Configuration

```go
config := sortablenano.Config{
    // Custom alphabet (default: "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ-_")
    Alphabet: "0123456789ABCDEF",
    
    // Total length of generated IDs (default: 32)
    TotalLength: 20,
    
    // Start date for timestamp calculation (default: 2024-01-01)
    TimestampStart: time.Date(2024, 1, 1, 0, 0, 0, 0, time.UTC),
    
    // Timestamp precision level (default: Microsecond)
    TimestampLevel: sortablenano.Millisecond,
    
    // Maximum rate at which IDs remain sortable (default: Micro100)
    MaxSortableRate: sortablenano.Milli10,
}
```

### Timestamp Levels

Choose the precision of your timestamps:
```go
sortablenano.Nanosecond  // Nanosecond precision
sortablenano.Microsecond // Microsecond precision (default)
sortablenano.Millisecond // Millisecond precision
sortablenano.Second      // Second precision
sortablenano.Minute      // Minute precision
sortablenano.Hour        // Hour precision
sortablenano.Day         // Day precision
sortablenano.Month       // Month precision
sortablenano.Year        // Year precision
```

### Maximum Sortable Rates

Available generation rates (choose based on your requirements):
```go
sortablenano.Nano10    // 10 generations per nanosecond
sortablenano.Micro100  // 100 generations per microsecond (default)
sortablenano.Micro1    // 1 generation per microsecond
sortablenano.Milli10   // 10 generations per millisecond
sortablenano.Second100 // 100 generations per second
sortablenano.Second1   // 1 generation per second
```

## Common Use Cases

### High-Performance System IDs

```go
config := sortablenano.Config{
    TimestampLevel:  sortablenano.Microsecond,
    MaxSortableRate: sortablenano.Micro100, // 100 IDs per microsecond
    TotalLength:     32,
}
```

### Short URLs

```go
config := sortablenano.Config{
    Alphabet:       "0123456789abcdefghijklmnopqrstuvwxyz",
    TotalLength:    12,
    TimestampLevel: sortablenano.Minute,
}
```

## Best Practices

1. **Choose TimestampLevel Based on Needs**:
   - Use `Millisecond` for general purposes
   - Use `Microsecond` or `Nanosecond` for high-precision requirements
   - Use `Day` or `Month` for long-term identifiers
   - The more long-term identifiers you choose, the less timestamplength you can use.

2. **Set MaxSortableRate Appropriately**:
   - Higher rates require more space in the ID
   - Choose a rate that comfortably exceeds your peak requirements
   - Consider using a lower rate if ID length is a concern

3. **Optimize ID Length**:
   - Choose appropriate `TimestampStart` for your application
   - Choose the minimum required `TimestampLevel`
   - Balance between `MaxSortableRate` and total length
   - Balance between `TimestampLevel` and total length

4. **Handle Errors**:
   - Always check for errors from `Generate()`
   - Consider implementing retry logic for rate limit errors
   - Use `PrintInfo()` to debug configuration issues

## Thread Safety

The generator is thread-safe and can be safely used concurrently from multiple goroutines.

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. 