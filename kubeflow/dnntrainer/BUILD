load("@bazel_tools//tools/build_defs/pkg:pkg.bzl", "pkg_tar", "pkg_deb")
load("//:global_vars.bzl", "PROJECT_ID", "GCS_BUCKET", "REPO_NAME", "KFP_HOST", "KFP_CLIENT_ID")
load("//common/build_rules:pipeline_creator.bzl", "pipeline_creator")
load("//common/build_rules:ref_replacer.bzl", "ref_replacer")
load("//common/build_rules:pipeline_test.bzl", "pipeline_test")


# -----------------------------------------------------------------
# No need to modify in most cases
# Define common variables
# -----------------------------------------------------------------
COMPONENT_NAME = REPO_NAME + "/" + package_name()

TARGET_FOLDER = select({
    "//common/build_rules:dry_run_mode": "/tmp" + "/" + COMPONENT_NAME,
    "//common/build_rules:prod_mode": GCS_BUCKET + "/" + COMPONENT_NAME,
    "//conditions:default": "/tmp" + "/" + COMPONENT_NAME,
})


# -----------------------------------------------------------------
# No need to modify in most cases
# -----------------------------------------------------------------
pkg_tar(
    name = "release_tar",
    srcs = [":release_files"],
    extension = "tar.gz",
    strip_prefix = ".",
)

filegroup(
    name = "release_files",
    srcs = [
        ":release_component_yaml",
    ],
)


# ------------------------------------------------------------------
# To be updated
# ------------------------------------------------------------------
# release component yaml
ref_replacer(
    name = "release_component_yaml",
    srcs = ["component.yaml"],
    outs = ["RELEASE_component.yaml"],
    container_refs = [
        "gcr.io/{}/{}:latest".format(PROJECT_ID, COMPONENT_NAME),
    ],
    str_replace_json = {
        "__CONTAINER_URI__": "gcr.io/{}/{}:latest".format(PROJECT_ID, COMPONENT_NAME),
    },
)

# Create pipeline files
pipeline_creator(
    name = "create_pipeline",
    src = ":release_component_yaml",
    out = "pipeline.tar.gz",
    use_gcp_secret = True,
)
