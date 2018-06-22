# CellFishing.jl ꙮ 🎣

CellFishing.jl (**cell** **fi**nder by ha**shing**) is a tool to find similar cells of query cells based on their transcriptome expression profiles.

```julia
# Import packages.
using CellFishing
using CSV

# Load expression profiles of database cells.
data = CSV.read("database.txt", delim='\t')
cellnames = string.(names(data))
featurenames = string.(data[:,1])
counts = Matrix{Float32}(data[:,2:end])

# Select features and create an index (or a database).
selected = CellFishing.selectfeatures(counts, featurenames)
index = CellFishing.CellIndex(counts, selected, metadata=cellnames)

# Save/load the database to/from a file (optional).
# CellFishing.save("database.cf", index)
# run(`gzip database.cf`)
# index = CellFishing.load("database.cf.gz")

# Load expression profiles of query cells.
data = CSV.read("query.txt", delim='\t')
cellnames = string.(names(data))
featurenames = string.(data[:,1])
counts = Matrix{Float32}(data[:,2:end])

# Search the database for similar cells.
k = 10
neighbors = CellFishing.findknn(k, counts, featurenames, index)

# Write the neighboring cells to a file.
open("neighbors.tsv", "w") do file
    println(file, join(["cell"; string.("n", 1:k)], '\t'))
    for j in 1:length(cellnames)
        print(file, cellnames[j])
        for i in 1:k
            print(file, '\t', index.metadata[neighbors.indexes[i,j]])
        end
        println(file)
    end
end
```
