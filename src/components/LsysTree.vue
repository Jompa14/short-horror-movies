<template>
  <group>
    <line v-for="(line, index) in lines" :key="index">
      <lineBasicMaterial :color="lineColor" />
      <geometry :vertices="line" />
    </line>
    <points v-if="leavesVisible">
      <pointsMaterial :color="leavesColor" :size="leavesSize" />
      <geometry :vertices="leaves" />
    </points>
  </group>
</template>

<script>
import * as THREE from 'three';

export default {
  props: {
    iterations: {
      type: Number,
      default: 5
    },
    angle: {
      type: Number,
      default: Math.PI / 8
    },
    length: {
      type: Number,
      default: 0.1
    },
    lineColor: {
      type: String,
      default: "#00ff00"
    },
    leavesVisible: {
      type: Boolean,
      default: true
    },
    leavesColor: {
      type: String,
      default: "#ff0000"
    },
    leavesSize: {
      type: Number,
      default: 0.5
    },
    masterBranchSize: {
      type: Number,
      default: 1.0
    },
    childrenBranchSize: {
      type: Number,
      default: 0.5
    }
  },
  computed: {
    lines() {
      const lines = [];
      const masterBranchSize = this.masterBranchSize;
      const childrenBranchSize = this.childrenBranchSize;

      // Generate tree geometry based on the provided props
      // Example implementation:
      const trunk = [
        new THREE.Vector3(0, 0, 0),
        new THREE.Vector3(0, 1, 0)
      ];
      lines.push(trunk);

      const generateBranches = (startPoint, direction, length, size, iteration) => {
        if (iteration === 0) return;

        const endPoint = startPoint.clone().add(direction.clone().multiplyScalar(length));
        const branch = [startPoint.clone(), endPoint.clone()];
        lines.push(branch);

        const leftDirection = direction.clone().applyAxisAngle(new THREE.Vector3(0, 0, 1), this.angle);
        const rightDirection = direction.clone().applyAxisAngle(new THREE.Vector3(0, 0, -1), this.angle);

        generateBranches(endPoint, leftDirection, length * 0.8, size * childrenBranchSize, iteration - 1);
        generateBranches(endPoint, rightDirection, length * 0.8, size * childrenBranchSize, iteration - 1);
      };

      generateBranches(new THREE.Vector3(0, 1, 0), new THREE.Vector3(0, 1, 0), this.length, masterBranchSize, this.iterations);

      return lines;
    },
    leaves() {
      const leaves = [];

      // Generate leaf geometry based on the provided props
      // Example implementation:
      const leafPosition = new THREE.Vector3(0, 1, 0);
      leaves.push(leafPosition);

      return leaves;
    }
  }
};
</script>
