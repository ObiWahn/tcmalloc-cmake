#!/usr/bin/env bash

out="../tcmalloc_files.cmake"

cd tcmalloc || exit 1;
true > "$out"

{
echo "set(TCMALLOC_FILES"
find ./tcmalloc -maxdepth 1 -regex ".*\.\(cc\|h\)" \
    | sort | grep -v -E "mock_|_test|_fuzz|_benchmark|profile_marshaler.cc"
echo -e ")\n\n"

echo "set(TCMALLOC_TEST_FILES"
find ./tcmalloc -maxdepth 1 -regex ".*\.\(cc\|h\)" \
    | sort | grep -E "mock_|_test|_fuzz|_benchmark|profile_marshaler.cc"
echo -e ")\n\n"


echo "set(TCMALLOC_INTERNAL_FILES"
find ./tcmalloc/internal -maxdepth 1 -regex ".*\.\(cc\|h\)" \
    | sort | grep -v -E "_test|_fuzz|_benchmark|profile_builder.cc"
echo -e ")\n\n"

echo "set(TCMALLOC_INTERNAL_TEST_FILES"
find ./tcmalloc/internal -maxdepth 1 -regex ".*\.\(cc\|h\)" \
    | sort | grep -E "_test|_fuzz|_benchmark|profile_builder.cc"
echo -e ")\n\n"


echo "set(TCMALLOC_TESTING_FILES"
find ./tcmalloc/testing -maxdepth 1 -regex ".*\.\(cc\|h\)" | sort
echo ")"

} >> $out
