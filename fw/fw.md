# Plans for future:
*This page discusses the work remaining, especially the plan for parallel parsing flatbuffer input format.*

## Parallel parsing flatbuffers:

### Why flatbuffers:
Compared to text and JSON, flatbuffers outperform them in speed. They come very close to cache input
format in terms of speed (checked locally on a 400k size dataset). Here’s why:

When the input format is a flatbuffer, the parser’s job becomes as simple as just passing the feature to the
example struct. Since there is no form of conversion of data (unlike text, which needs to be parsed line by
line), the process becomes less expensive.

Apart from speed, flatbuffers provide structure and flexibility to the data stored, which is absent in cache.
There are other benefits also, like reuse of data in the buffer and cross-platform support. Hence, flatbuffers
are important for VW, and it would be good to provide multithreaded parsing support for the same.

### Approach:

Separating the io component will be straightforward for Flatbuffers. This is because the object_size is
already stored as a read_prefix [here](https://github.com/nishantkr18/vowpal_wabbit/blob/master/vowpalwabbit/parser/flatbuffer/parse_example_flatbuffer.cc#L44). This basically tells us the number of characters to be read from the
buffer for the current example. So, we just need to move [these](https://github.com/nishantkr18/vowpal_wabbit/blob/master/vowpalwabbit/parser/flatbuffer/parse_example_flatbuffer.cc#L39:#L47) lines from the `parse_example_flatbuffer.cc`
file, to the `io_to_queue.h` file, which takes care of the I/O.

#### IO thread:
Once the io_thread knows how many characters to read, it can read those and push them as a vector of
chars, to the io_lines queue. So a function `read_input_file_flatbuff` can be called repeatedly by the io_thread
till no characters are left to read in the buffer.

```c++
inline bool read_input_file_flatbuff(vw& all, char *&line) {
char* line = nullptr;
// Read the first four bytes.
auto len = all.example_parser->input->buf_read(line, sizeof(uint32_t));
// If no character read, return true (should_finish = true)
if (len < sizeof(uint32_t)) { return true; }
// Read the object size from the prefix.
auto _object_size = flatbuffers::ReadScalar<flatbuffers::uoffset_t>(line);
// Read the object.
all.example_parser->input->buf_read(line, _object_size);
// convert it to a vector of characters
std::vector<char> *byte_array = new std::vector<char>();
byte_array->resize(_object_size);
memcpy(byte_array->data(), line, _object_size);
// Push it to the io_lines
all.example_parser->io_lines.push(std::move(byte_array));
return false;
}
```

#### Parser thread:
The flatbuffer parser can now be easily multithreaded, just like the text parser. We just need to spawn
multiple threads, and perform these two operations atomically in the beginning:

1. Pop a line from the io_lines queue.
2. Push an empty, unparsed example to the ready_parsed_example queue.

And when the parsing is complete, we need to call `notify_examples_cv()` with the current example, so that a
waiting learner thread can start its work.
The only major change therefore will take place in the `parse_example_flatbuffer.cc` file. Specifically, this
function changes to:

```c++
ool parser::parse(vw* all, uint8_t* buffer_pointer, v_array<example*>& examples)
{
// No changes.
if (buffer_pointer)
{
_flatbuffer_pointer = buffer_pointer;
_data = VW::parsers::flatbuffer::GetSizePrefixedExampleRoot(_flatbuffer_pointer);
return true;
}
// Atomically popping from io_lines and pushing an empty example.
std::vector<char> *line;
{
std::lock_guard<std::mutex> lck((*all).example_parser->parser_mutex);
line = all->example_parser->io_lines.pop();
if(line != nullptr) {
(*all).example_parser->ready_parsed_examples.push(examples[0]);
} else { return false; }
}
// Using the line popped from io_lines queue for further operations.
_flatbuffer_pointer = reinterpret_cast<uint8_t*>(*line);
_data = VW::parsers::flatbuffer::GetExampleRoot(_flatbuffer_pointer);
return true;
}
```

## Other stuff:
* Improving the 1-parser cache performance (to `~10%` of ST)
* Creating more extensive tests to identify contentions.
* Modifying the unit tests to account for multithreaded operations.
* Creating a PR to the VW master branch, with all issues sorted.
