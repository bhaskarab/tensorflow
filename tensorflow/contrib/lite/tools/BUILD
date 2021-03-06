package(default_visibility = [
    "//visibility:public",
])

licenses(["notice"])  # Apache 2.0

load("//tensorflow/contrib/lite:special_rules.bzl", "tflite_portable_test_suite")
load("//tensorflow:tensorflow.bzl", "tf_cc_binary")

common_copts = ["-Wall"]

py_binary(
    name = "visualize",
    srcs = ["visualize.py"],
    data = [
        "//tensorflow/contrib/lite/schema:schema.fbs",
        "@flatbuffers//:flatc",
    ],
    srcs_version = "PY2AND3",
)

tf_cc_binary(
    name = "generate_op_registrations",
    srcs = ["gen_op_registration_main.cc"],
    deps = [
        "//tensorflow/contrib/lite/tools:gen_op_registration",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "@com_google_absl//absl/strings",
    ],
)

tf_cc_binary(
    name = "benchmark_model",
    srcs = [
        "benchmark_main.cc",
        "logging.h",
    ],
    copts = common_copts,
    linkopts = select({
        "//tensorflow:android": [
            "-pie",
            "-landroid",
            "-lm",
            "-z defs",
            "-Wl,--exclude-libs,ALL",  # Exclude syms in all libs from auto export
        ],
        "//conditions:default": [],
    }),
    deps = [
        ":benchmark_tflite_model_lib",
        "//tensorflow/core:stats_calculator_portable",
    ],
)

cc_library(
    name = "command_line_flags",
    srcs = ["command_line_flags.cc"],
    hdrs = ["command_line_flags.h"],
    copts = common_copts,
    visibility = ["//visibility:private"],
)

cc_test(
    name = "command_line_flags_test",
    srcs = ["command_line_flags_test.cc"],
    copts = common_copts,
    visibility = ["//visibility:private"],
    deps = [
        ":command_line_flags",
        "//tensorflow/contrib/lite/testing:util",
        "@com_google_googletest//:gtest",
    ],
)

cc_library(
    name = "benchmark_tflite_model_lib",
    srcs = [
        "benchmark_tflite_model.cc",
        "logging.h",
    ],
    hdrs = ["benchmark_tflite_model.h"],
    copts = common_copts,
    deps = [
        ":benchmark_model_lib",
        "//tensorflow/contrib/lite:framework",
        "//tensorflow/contrib/lite:string_util",
        "//tensorflow/contrib/lite/kernels:builtin_ops",
        "//tensorflow/contrib/lite/profiling:profile_summarizer",
        "//tensorflow/contrib/lite/profiling:profiler",
    ],
)

cc_library(
    name = "benchmark_model_lib",
    srcs = [
        "benchmark_model.cc",
        "logging.h",
    ],
    hdrs = ["benchmark_model.h"],
    copts = common_copts,
    deps = [
        ":command_line_flags",
        "//tensorflow/contrib/lite:framework",
        "//tensorflow/contrib/lite:string_util",
        "//tensorflow/contrib/lite/kernels:builtin_ops",
        "//tensorflow/contrib/lite/profiling:profile_summarizer",
        "//tensorflow/contrib/lite/profiling:profiler",
        "//tensorflow/contrib/lite/profiling:time",
        "//tensorflow/core:stats_calculator_portable",
    ],
)

cc_library(
    name = "gen_op_registration",
    srcs = ["gen_op_registration.cc"],
    hdrs = ["gen_op_registration.h"],
    deps = [
        "//tensorflow/contrib/lite:framework",
        "//tensorflow/contrib/lite:string",
        "@com_googlesource_code_re2//:re2",
    ],
)

cc_test(
    name = "gen_op_registration_test",
    srcs = ["gen_op_registration_test.cc"],
    data = [
        "//tensorflow/contrib/lite:testdata/0_subgraphs.bin",
        "//tensorflow/contrib/lite:testdata/2_subgraphs.bin",
        "//tensorflow/contrib/lite:testdata/empty_model.bin",
        "//tensorflow/contrib/lite:testdata/test_model.bin",
        "//tensorflow/contrib/lite:testdata/test_model_broken.bin",
    ],
    tags = [
        "tflite_not_portable_android",
    ],
    deps = [
        ":gen_op_registration",
        "@com_google_googletest//:gtest",
    ],
)

cc_library(
    name = "verifier",
    srcs = ["verifier.cc"],
    hdrs = ["verifier.h"],
    deps = [
        "//tensorflow/contrib/lite:framework",
        "//tensorflow/contrib/lite:schema_fbs_version",
        "//tensorflow/contrib/lite:string_util",
        "//tensorflow/contrib/lite/schema:schema_fbs",
    ],
)

cc_test(
    name = "verifier_test",
    size = "small",
    srcs = ["verifier_test.cc"],
    tags = [
        "tflite_not_portable",
    ],
    deps = [
        ":verifier",
        "//tensorflow/contrib/lite:framework",
        "//tensorflow/contrib/lite:schema_fbs_version",
        "//tensorflow/contrib/lite/schema:schema_fbs",
        "//tensorflow/contrib/lite/testing:util",
        "//tensorflow/core:framework_lite",
        "@com_google_googletest//:gtest",
        "@flatbuffers",
    ],
)

tflite_portable_test_suite()
