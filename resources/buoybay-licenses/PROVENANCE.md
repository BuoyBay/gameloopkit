# Provenance

An independent integration layer over this repository's PhysicsKit and SpriteKit.
It uses the conventional fixed-step accumulator and previous/current position
interpolation pattern; no external engine source or artwork was copied. Original
REALbasic examples motivated the earlier packages but do not define this API.

This package intentionally keeps clock sampling and UI scheduling in the caller,
making clock partitions, overload/drop policy and pause behavior testable without
a display or real elapsed-time sleeps.
