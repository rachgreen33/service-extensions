licenses(["notice"])  # Apache 2

package(default_visibility = ["//visibility:public"])

cc_library(
    name = "framework",
    testonly = 1,
    srcs = ["framework.cc"],
    hdrs = ["framework.h"],
    deps = [
        "@boost//:algorithm",
        "@boost//:dll",
        "@boost//:filesystem",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_googletest//:gtest",
        "@proxy_wasm_cpp_host//:base_lib",
        "@proxy_wasm_cpp_host//test:utility_lib",
    ],
)

cc_library(
    name = "dynamic_test",
    testonly = 1,
    srcs = ["dynamic_test.cc"],
    hdrs = ["dynamic_test.h"],
    deps = [
        ":framework",
        ":runner_cc_proto",
        "@com_google_absl//absl/random",
        "@com_google_absl//absl/random:distributions",
        "@com_github_google_quiche//quiche:balsa",
        "@com_google_benchmark//:benchmark",
        "@com_google_googletest//:gtest",
        "@com_google_protobuf//:protobuf",
        "@com_google_re2//:re2",
        "@com_google_tcmalloc//tcmalloc:malloc_extension",
    ],
)

# --- THIS IS THE FIX ---
# Create wrapper libraries for each engine with `alwayslink = 1`.
# This forces the linker to include the engine's self-registration code,
# preventing it from being optimized away.

cc_library(
    name = "_v8_engine",
    deps = ["@proxy_wasm_cpp_host//:v8_lib"],
    alwayslink = 1,
)

cc_library(
    name = "_wamr_engine",
    deps = ["@proxy_wasm_cpp_host//:wamr_lib"],
    alwayslink = 1,
)

cc_binary(
    name = "runner",
    testonly = 1,
    srcs = ["runner_main.cc"],
    deps = [
        ":dynamic_test",
        ":framework",
        ":runner_cc_proto",
        # Add the alwayslink wrapper libraries as dependencies.
        ":_v8_engine",
        ":_wamr_engine",
        "@com_google_absl//absl/flags:flag",
        "@com_google_absl//absl/flags:parse",
        "@com_google_benchmark//:benchmark",
        "@com_google_googletest//:gtest",
        "@com_google_protobuf//:protobuf",
        "@com_google_tcmalloc//tcmalloc:tcmalloc",
    ],
)

proto_library(
    name = "runner_proto",
    srcs = ["runner.proto"],
)

cc_proto_library(
    name = "runner_cc_proto",
    deps = [":runner_proto"],
)