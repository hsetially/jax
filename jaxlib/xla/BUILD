# Copyright 2025 The JAX Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

load(
    "//jaxlib:jax.bzl",
    "cc_proto_library",
    "if_oss",
    "jax_visibility",
    "nanobind_extension",
    "py_deps",
    "py_strict_library",
    "py_strict_test",
    "pytype_strict_library",
)
# Placeholder: load proto_library

licenses(["notice"])

package(
    default_applicable_licenses = [],
    default_visibility = ["//jax:internal"],
)

package_group(
    name = "xla_python",
    includes = [
        "//jax:internal",
    ],
)

nanobind_extension(
    name = "xla_extension",
    srcs = ["xla.cc"],
    pytype_deps = py_deps(["numpy"]),
    pytype_srcs = glob(["xla_extension/*.pyi"]),
    visibility = ["//visibility:public"],
    deps = [
        ":config",
        ":custom_call_sharding",
        ":dlpack",
        ":jax_jit",
        ":mlir",
        ":pjit",
        ":pmap_lib",
        ":pytree",
        ":sdy",
        ":weakref_lru_cache",
        ":xla_compiler",
        "@com_google_absl//absl/base",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/log:initialize",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/time",
        "@com_google_absl//absl/types:span",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:IR",
        "@nanobind",
        "@tsl//tsl/platform",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla:literal",
        "@xla//xla:shape_util",
        "@xla//xla:types",
        "@xla//xla:util",
        "@xla//xla/backends/cpu/collectives:cpu_collectives",
        "@xla//xla/ffi:ffi_api",
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/pjrt:mlir_to_hlo",
        "@xla//xla/pjrt:pjrt_api",
        "@xla//xla/pjrt:pjrt_c_api_client",
        "@xla//xla/pjrt:pjrt_client",
        "@xla//xla/pjrt:pjrt_common",
        "@xla//xla/pjrt:pjrt_compiler",
        "@xla//xla/pjrt:pjrt_executable",
        "@xla//xla/pjrt:pjrt_layout",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/pjrt/c:pjrt_c_api_hdrs",
        "@xla//xla/pjrt/distributed",
        "@xla//xla/pjrt/distributed:client",
        "@xla//xla/pjrt/distributed:key_value_store_interface",
        "@xla//xla/pjrt/distributed:protocol_proto_cc",
        "@xla//xla/pjrt/distributed:service",
        "@xla//xla/pjrt/plugin/xla_cpu:cpu_client_options",
        "@xla//xla/pjrt/plugin/xla_cpu:xla_cpu_pjrt_client",
        "@xla//xla/python:guard_lib",
        "@xla//xla/python:logging",
        "@xla//xla/python:nb_absl_flat_hash_map",
        "@xla//xla/python:nb_absl_span",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/python:ops",
        "@xla//xla/python:pprof_profile_builder",
        "@xla//xla/python:profiler",
        "@xla//xla/python:py_client",
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/python:refine_polymorphic_shapes",
        "@xla//xla/python:traceback",
        "@xla//xla/python:types",
        "@xla//xla/python:util",
        "@xla//xla/python/ifrt",
        "@xla//xla/python/ifrt:plugin_program",
        "@xla//xla/python/ifrt:plugin_program_serdes",
        "@xla//xla/python/ifrt_proxy/client:py_module",
        "@xla//xla/python/pjrt_ifrt",
        "@xla//xla/python/pjrt_ifrt:pjrt_attribute_map_util",
        "@xla//xla/python/pjrt_ifrt:xla_ifrt",
        "@xla//xla/tsl/concurrency:ref_count",
        "@xla//xla/tsl/distributed_runtime/preemption:preemption_sync_manager",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:status",
        "@xla//xla/tsl/platform:statusor",
        "@xla//xla/tsl/platform/cloud:gcs_file_system",
        "@xla//xla/tsl/python/lib/core:numpy",
    ] + select({
        # gloo tcp transport only builds on linux
        "@xla//xla/tsl:macos": [
            "@gloo//:transport_uv",
            "@xla//xla/backends/cpu/collectives:gloo_collectives",
            "@xla//xla/backends/cpu/collectives:gloo_kv_store",
        ],
        "@xla//xla/tsl:windows": [],
        "//conditions:default": [
            ":py_socket_transfer",
            "@gloo//:transport_tcp",
            "@xla//xla/backends/cpu/collectives:gloo_collectives",
            "@xla//xla/backends/cpu/collectives:gloo_kv_store",
        ],
    }) + select({
        # mpitrampoline does not build on windows
        "@xla//xla/tsl:windows": [],
        # we support MPI collectives only in OSS builds
        "//conditions:default": if_oss(["@xla//xla/backends/cpu/collectives:mpi_collectives"]),
    }),
)

cc_library(
    name = "config",
    srcs = ["config.cc"],
    hdrs = ["config.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:flat_hash_set",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/tsl/platform:logging",
    ],
)

cc_library(
    name = "custom_call_sharding",
    srcs = ["custom_call_sharding.cc"],
    hdrs = ["custom_call_sharding.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@nanobind",
        "@xla//xla:shape_util",
        "@xla//xla:util",
        "@xla//xla/hlo/ir:hlo",
        "@xla//xla/hlo/utils:hlo_sharding_util",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/pjrt/c:pjrt_c_api_custom_partitioner_extension_hdrs",
        "@xla//xla/pjrt/c:pjrt_c_api_hdrs",
        "@xla//xla/pjrt/c:pjrt_c_api_helpers",
        "@xla//xla/python:custom_call_batch_partitioner",
        "@xla//xla/python:custom_partition_callback",
        "@xla//xla/python:inspect_sharding",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "dlpack",
    srcs = ["dlpack.cc"],
    hdrs = ["dlpack.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/types:span",
        "@dlpack",
        "@llvm-project//llvm:Support",
        "@nanobind",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla:shape_util",
        "@xla//xla:status_macros",
        "@xla//xla:util",
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/pjrt:pjrt_client",
        "@xla//xla/pjrt:pjrt_common",
        "@xla//xla/pjrt:pjrt_compiler",
        "@xla//xla/pjrt:pjrt_layout",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/python:py_client",
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/python:traceback",
        "@xla//xla/python:types",
        "@xla//xla/python:util",
        "@xla//xla/python/ifrt",
        "@xla//xla/python/pjrt_ifrt",
        "@xla//xla/tsl/platform:errors",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "jax_jit",
    srcs = ["jax_jit.cc"],
    hdrs = ["jax_jit.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":pytree",
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@tsl//tsl/profiler/lib:traceme",
        "@xla//third_party/python_runtime:headers",  # build_cleaner: keep
        "@xla//xla/pjrt:pjrt_client",
        "@xla//xla/pjrt:pjrt_layout",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/python:nb_absl_inlined_vector",
        "@xla//xla/python:nb_absl_span",
        "@xla//xla/python:nb_helpers",
        "@xla//xla/python:py_client",
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/python:types",
        "@xla//xla/tsl/platform:logging",
    ],
)

cc_library(
    name = "mlir",
    srcs = ["mlir.cc"],
    hdrs = ["mlir.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/log",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:string_view",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:BytecodeWriter",
        "@llvm-project//mlir:FuncDialect",
        "@llvm-project//mlir:FuncExtensions",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Parser",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:ReconcileUnrealizedCasts",
        "@llvm-project//mlir:Support",
        "@nanobind",
        "@stablehlo//:stablehlo_serialization",
        "@xla//xla/hlo/builder:xla_computation",
        "@xla//xla/hlo/translate:stablehlo",
        "@xla//xla/mlir_hlo:mhlo_passes",
        "@xla//xla/pjrt:mlir_to_hlo",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/python:refine_polymorphic_shapes",
        "@xla//xla/service/llvm_ir:llvm_util",
        "@xla//xla/tsl/platform:errors",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "pjit",
    srcs = ["pjit.cc"],
    hdrs = ["pjit.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":config",
        ":jax_jit",
        ":pytree",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/cleanup",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@tsl//tsl/profiler/lib:traceme",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla:shape_util",
        "@xla//xla:util",
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/pjrt:lru_cache",
        "@xla//xla/python:guard_lib",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/python:nb_helpers",
        "@xla//xla/python:nb_numpy",
        "@xla//xla/python:py_client",
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/python:traceback",
        "@xla//xla/python/ifrt",
        "@xla//xla/tsl/concurrency:ref_count",
        "@xla//xla/tsl/platform:env",
        "@xla//xla/tsl/platform:errors",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
    ],
)

cc_library(
    name = "pmap_lib",
    srcs = ["pmap_lib.cc"],
    hdrs = ["pmap_lib.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":config",
        ":jax_jit",
        ":pytree",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/log:check",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@tsl//tsl/profiler/lib:traceme",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla:status_macros",
        "@xla//xla:util",
        "@xla//xla:xla_data_proto_cc",
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/python:nb_helpers",
        "@xla//xla/python:nb_numpy",
        "@xla//xla/python:py_client",
        "@xla//xla/python:python_ref_manager",
        "@xla//xla/python:traceback",
        "@xla//xla/python:types",
        "@xla//xla/python/ifrt",
        "@xla//xla/tsl/concurrency:ref_count",
        "@xla//xla/tsl/platform:env",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
        "@xla//xla/tsl/python/lib/core:numpy",
    ],
)

cc_library(
    name = "py_socket_transfer",
    srcs = ["py_socket_transfer.cc"],
    hdrs = ["py_socket_transfer.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@nanobind",
        "@xla//xla:util",
        "@xla//xla/pjrt:pjrt_client",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/python:nb_numpy",
        "@xla//xla/python:py_client",
        "@xla//xla/python:traceback",
        "@xla//xla/python:types",
        "@xla//xla/python/ifrt",
        "@xla//xla/python/pjrt_ifrt",
        "@xla//xla/python/pjrt_ifrt:pjrt_dtype",
        "@xla//xla/python/transfer:event_loop",
        "@xla//xla/python/transfer:socket-server",
        "@xla//xla/python/transfer:socket_bulk_transport",
        "@xla//xla/python/transfer:streaming",
        "@xla//xla/python/transfer:streaming_ifrt",
        "@xla//xla/tsl/concurrency:ref_count",
        "@xla//xla/tsl/platform:statusor",
    ],
)

proto_library(
    name = "pytree_proto",
    srcs = ["pytree.proto"],
)

cc_proto_library(
    name = "pytree_cc_proto",
    deps = [":pytree_proto"],
)

cc_library(
    name = "pytree",
    srcs = ["pytree.cc"],
    hdrs = ["pytree.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    visibility = jax_visibility("jaxlib/xla/pytree"),
    deps = [
        ":pytree_cc_proto",
        "@com_google_absl//absl/algorithm:container",
        "@com_google_absl//absl/container:flat_hash_map",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@xla//third_party/python_runtime:headers",  # buildcleaner: keep
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/python:nb_class_ptr",
        "@xla//xla/tsl/platform:logging",
    ],
)

cc_library(
    name = "sdy",
    srcs = ["sdy.cc"],
    hdrs = ["sdy.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/strings:string_view",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:BytecodeWriter",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:Support",
        "@nanobind",
        "@shardy//shardy/dialect/sdy/ir:dialect",
        "@shardy//shardy/dialect/sdy/transforms/import:passes",
        "@xla//xla/hlo/translate/hlo_to_mhlo:hlo_to_mlir_hlo",
        "@xla//xla/mlir_hlo:all_passes",
        "@xla//xla/pjrt:mlir_to_hlo",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/service/spmd/shardy:constants",
        "@xla//xla/service/spmd/shardy:utils",
        "@xla//xla/service/spmd/shardy/sdy_round_trip:import_shardy_attrs",
        "@xla//xla/service/spmd/shardy/sdy_round_trip:pipelines",
        "@xla//xla/tsl/framework/mlir:status_scoped_diagnostic_handler",
    ],
)

cc_library(
    name = "weakref_lru_cache",
    srcs = ["weakref_lru_cache.cc"],
    hdrs = ["weakref_lru_cache.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/cleanup",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/synchronization",
        "@nanobind",
        "@xla//third_party/python_runtime:headers",
        "@xla//xla/pjrt:lru_cache",
        "@xla//xla/tsl/platform:logging",
    ],
)

cc_library(
    name = "xla_compiler",
    srcs = ["xla_compiler.cc"],
    hdrs = ["xla_compiler.h"],
    compatible_with = [],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":dlpack",
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/container:inlined_vector",
        "@com_google_absl//absl/hash",
        "@com_google_absl//absl/status",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/strings:str_format",
        "@com_google_absl//absl/synchronization",
        "@com_google_absl//absl/types:span",
        "@nanobind",
        "@xla//xla:array",
        "@xla//xla:debug_options_flags",
        "@xla//xla:literal",
        "@xla//xla:shape_util",
        "@xla//xla:util",
        "@xla//xla:xla_data_proto_cc",
        "@xla//xla:xla_proto_cc",
        "@xla//xla/client:executable_build_options",
        "@xla//xla/ffi",
        "@xla//xla/ffi:ffi_api",
        "@xla//xla/ffi/api:c_api",
        "@xla//xla/hlo/builder:xla_builder",
        "@xla//xla/hlo/builder:xla_computation",
        "@xla//xla/hlo/ir:hlo",
        "@xla//xla/hlo/ir:hlo_module_group",
        "@xla//xla/hlo/parser:hlo_parser",
        "@xla//xla/hlo/pass:hlo_pass",
        "@xla//xla/hlo/transforms/simplifiers:flatten_call_graph",
        "@xla//xla/hlo/transforms/simplifiers:hlo_dce",
        "@xla//xla/hlo/transforms/simplifiers:tuple_simplifier",
        "@xla//xla/pjrt:compile_options_proto_cc",
        "@xla//xla/pjrt:exceptions",
        "@xla//xla/pjrt:pjrt_executable",
        "@xla//xla/pjrt:status_casters",
        "@xla//xla/python:nb_absl_span",
        "@xla//xla/python:nb_helpers",
        "@xla//xla/python:nb_numpy",
        "@xla//xla/python:py_client",
        "@xla//xla/python:types",
        "@xla//xla/service:call_inliner",
        "@xla//xla/service:computation_placer",
        "@xla//xla/service:custom_call_target_registry",
        "@xla//xla/service:hlo_graph_dumper",
        "@xla//xla/service:hlo_module_config",
        "@xla//xla/service:hlo_proto_cc",
        "@xla//xla/service:name_uniquer",
        "@xla//xla/tsl/lib/strings:proto_serialization",
        "@xla//xla/tsl/platform:env",
        "@xla//xla/tsl/platform:errors",
        "@xla//xla/tsl/platform:logging",
        "@xla//xla/tsl/platform:statusor",
    ],
)

pytype_strict_library(
    name = "xla_client",
    srcs = ["xla_client.py"],
    pytype_srcs = ["xla_client.pyi"],
    visibility = [":xla_python"],
    deps = py_deps([
        "numpy",
        "ml_dtypes",
    ]) + [":xla_extension"],
)

py_strict_test(
    name = "xla_client_backend_independent_test",
    srcs = ["xla_client_backend_independent_test.py"],
    deps = [
        ":xla_client",
    ] + py_deps([
        "absl/testing",
        "numpy",
        "portpicker",
    ]),
)

py_strict_library(
    name = "xla_client_test",
    testonly = 1,
    srcs = ["xla_client_test.py"],
    visibility = [":xla_python"],
    deps = [
        ":xla_client",
        "//jax",
        "//jax:test_util",
        "//jaxlib",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
        "ml_dtypes",
        "numpy",
    ]),
)

nanobind_extension(
    name = "custom_calls_testlib",
    testonly = 1,
    srcs = ["custom_calls_testlib.cc"],
    deps = [
        "@com_google_absl//absl/status",
        "@nanobind",
        "@xla//xla/ffi/api:c_api",
        "@xla//xla/ffi/api:ffi",
    ],
)

py_strict_test(
    name = "xla_client_test_cpu",
    srcs = ["xla_client_test.py"],
    args = ["--backend=cpu"],
    env = {
        "XLA_FLAGS": "--xla_force_host_platform_device_count=4",
    },
    main = "xla_client_test.py",
    deps = [
        ":custom_calls_testlib",
        ":xla_client",
        "//jax",
        "//jax:test_util",
        "//jaxlib",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
        "ml_dtypes",
        "numpy",
    ]),
)

py_strict_test(
    name = "weakref_lru_cache_test",
    srcs = ["weakref_lru_cache_test.py"],
    deps = [
        ":xla_client",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
    ]),
)

py_strict_test(
    name = "pytree_test",
    srcs = ["pytree_test.py"],
    deps = [
        ":xla_client",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
    ]),
)

py_strict_test(
    name = "config_test",
    srcs = ["config_test.py"],
    deps = [
        ":xla_client",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
    ]),
)

py_strict_test(
    name = "jax_jit_test",
    srcs = ["jax_jit_test.py"],
    deps = [
        ":xla_client",
    ] + py_deps([
        "absl/flags",
        "absl/logging",
        "absl/testing",
        "numpy",
    ]),
)
